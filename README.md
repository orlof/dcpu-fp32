<h1>DCPU-FP32</h1>

<h5><i>3rd Generation Floating Point library for DCPU</i></h5>
--

Provides basic floating point arithmetic for DCPU. Floating point numbers are represented in proprietary format that provides 8 bit exponent and 32 bit mantissa (only 31 significant bits as msb is always 1).

<h6>Design Philosophy</h6>
 - Easy API for integration into operating systems and applications
 - Small and simple codebase

<h6>Implementation Principles</h6>
 - float parameters are passed in stack by pointer
 - int16 return value is passes in stack
 - parameters are pushed to stack in right to left order
 - caller is responsible for cleaning the stack (arguments and possible return value)
 - registers a, b, c, i, j, x, y and z are preserved
 - same pointer can be used as any or all arguments

<h6>SHORTCOMINGS</h6>
 - Support only truncate rounding
 - No denormalized numbers
 - Both exponent's and significant's bit widths are hardcoded
 - Division uses slow long division algorithm

--

<h6>Installation</h6>

Required files:

    defs.dasm16

    float.dasm16

Test files (not needed):

    test_float.dasm16
    
    dunit.dasm16

--

<h6>API</h6>

    int16 float_cmp(float *left, float *right)
    void float_add(float *left, float *right, float *result)
    void float_sub(float *left, float *right, float *result)
    void float_mul(float *left, float *right, float *result)
    void float_div(float *left, float *right, float *result)
    uint16 float_to_uint16(float *src)
    void float_from_uint16(uint16 value, float *dst)
    void float_negate(float *src, float *dst)
    void float_abs(float *src, float *dst)

<h6>Candidates for future development</h6>

    float_from_str
    float_to_str
    sqrt
    pow
    sin, cos, tan

<h6>Example</h6>

    ; calculate (2 + 4) * 2
    
    #include "defs.dasm16"
    
    :f_result dat 0,0,0,0
    :f_2      dat FLOAT_TYPE_PNUM, 0x8001, 0x8000, 0x0000
    :f_4      dat FLOAT_TYPE_PNUM, 0x8002, 0x8000, 0x0000
    
    ; API calls
    set push, f_result      ; push result address
    set push, f_4           ; push 4.0 address
    set push, f_2           ; push 2.0 address
    jst float_add
    
    add sp, 3               ; cleanup stack
    
    set push, f_result      ; push result address
    set push, f_2           ; push 2.0 address
    set push, f_result      ; push (2 + 4) result address
                          ; same address can be used as multiple parameters
    jsr float_mul
    
    add sp, 3               ; cleanup stack
    
    ; f_result now contains 12.0

--

<h6>Data Format</h6>

Each floating point number takes 4 words.

    [0] FLOAT_TYPE:
        FLOAT_TYPE_NAN   0x8000
        FLOAT_TYPE_PINF  -3
        FLOAT_TYPE_PNUM  -2
        FLOAT_TYPE_PZERO -1
        FLOAT_TYPE_DBZ    0     (Divide By Zero error)
        FLOAT_TYPE_NZERO  1
        FLOAT_TYPE_NNUM   2
        FLOAT_TYPE_NINF   3
    [1] FLOAT_EXP
        Exponent with 0x8000 bias (1.0 has exponent 0x8000)
        NA if FLOAT_TYPE is not NNUM or PNUM
    [2-3] FLOAT_HIGH:FLOAT_LOW
        Normalized mantissa with msb always set
        NA if FLOAT_TYPE is not NNUM or PNUM

