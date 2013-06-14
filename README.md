--
<h1>DCPU-FP32</h1>
<h5><i>3rd Generation Floating Point library for 0x10c DCPU</i></h5>
--

<h4>Design Philosophy</h4>
 - Easy integration to operating systems and other applications
 - Speed over memory consumption

<h4>Implementation Prinsiples</h4>
 - parameters are passed by reference in stack
 - parameters are pushed to stack in right to left order
 - caller is responsible for cleaning the stack
 - int16 return value is passes in stack
 - registers a, b, c, i, j, x, y and z are preserved

--

<h4>Installation</h4>

Required files:

    defs.dasm16

    float.dasm16

Test files (not needed):

    test_float.dasm16
    
    dunit.dasm16

--

<h4>API</h4>

Currently supported operations:
    int16 float_cmp(float *left, float *right)
    void float_add(float *left, float *right, float *result)
    void float_sub(float *left, float *right, float *result)
    void float_mul(float *left, float *right, float *result)

<pre>
Example (2 + 3) * 3
  set push, float_left    ; rightmost argument: result
  set push, float_right   ; second argument: right
  set push, float_left    ; leftmost argument: left
  jsr float_add
  jsr float_mul           ; result is now in float_left
  add sp, 3
</pre>
--

<h4>Number Format</h4>

Each floating point number takes 4 words

    [0] FLOAT_TYPE:
        #define FLOAT_TYPE_NAN   0x8000
        #define FLOAT_TYPE_PINF  -3
        #define FLOAT_TYPE_PNUM  -2
        #define FLOAT_TYPE_PZERO -1
        #define FLOAT_TYPE_NZERO  1
        #define FLOAT_TYPE_NNUM   2
        #define FLOAT_TYPE_NINF   3
    [1] FLOAT_EXP
        Exponent with 0x7fff bias (1.0 has exponent 0x7fff)
    [2-3] FLOAT_HIGH:FLOAT_LOW
        Normalized mantissa with msb always set

