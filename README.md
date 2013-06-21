<h1>DCPU-FP32</h1>
<h5><i>3rd Generation Floating Point library for 0x10c DCPU</i></h5>
--

<h6>Design Philosophy</h6>
 - Easy integration to DCPU's operating systems and applications
 - Easile maintainable code base
 - Speed over memory consumption

<h6>Implementation Principles</h6>
 - parameters are passed by reference in stack
 - parameters are pushed to stack in right to left order
 - caller is responsible for cleaning the stack
 - int16 return value is passes in stack
 - registers a, b, c, i, j, x, y and z are preserved

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

<pre>
Running Tested Features
  int16 float_cmp(float *left, float *right)
  void float_add(float *left, float *right, float *result)
  void float_sub(float *left, float *right, float *result)
  void float_mul(float *left, float *right, float *result)
  void float_div(float *left, float *right, float *result)

Implemented, but not tested
  uint16 float_from_uint16(float *src)
  void float_from_uint16(uint16 value, float *dst)
  void float_negate(float *src, float *dst)
  void float_negate(float *src, float *dst)

Candidates for future development
  sqrt
  pow
  sin, cos, tan
  float_from_str
  float_to_str
  

Example (2 + 3) * 3

  ; simple implementation
  set push, float_result
  set push, float_3
  set push, float_2
  jst float_add
  
  add sp, 3               ; cleanup

  set push, float_result
  set push, float_3
  set push, float_result
  jsr float_mul
  
  add sp, 3
  

  ; optimized implementation
  set push, float_result
  set push, float_3
  set push, float_2
  jsr float_add

  set [sp], float_result
  jsr float_mul
  add sp, 3
</pre>
--

<h6>Number Format</h6>

Each floating point number takes 4 words

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

