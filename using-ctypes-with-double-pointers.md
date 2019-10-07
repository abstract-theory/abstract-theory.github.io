
#Using Ctypes with double pointers
##September 4, 2018


There is very little on the web describing how to pass data to a C function that has a double pointer as an input variable. The following example passes a 2D Numpy matrix to a C library using ctypes.

Below is a function that has a double pointer as an input variable.

```c
    // my_lib.c

    #include <stdio.h>

    void cFunc (int **x, int xLim, int yLim)
    {
        int nx, ny;
        for (nx = 0; nx < xLim; nx++)
            for (ny = 0; ny < yLim; ny++)
                x[nx][ny] = nx+ny*100;
    }

To compile with Linux/GCC, run the below commands.

```sh
    gcc -c -fPIC -O3 my_lib.c
    gcc -shared -Wl,-soname,my_lib.so -o my_lib.so my_lib.o

This is a Python function that passes a matrix to the C function.

```python
    # dbl_ptr_example.py

    import numpy as np
    import ctypes as ct

    lib = ct.CDLL('./my_lib.so')

    toDblPtr = lambda x: x.ctypes.data + x.strides[0]*np.arange(x.shape[0], dtype=np.uintp)

    def callCLib(matrix):
        [xSz, ySz] = matrix.shape
        lib.cFunc.restype = None
        lib.cFunc.argtypes = [np.ctypeslib.ndpointer(dtype=np.uintp, ndim=1)] +  [ct.c_uint]*2
        lib.cFunc(toDblPtr(matrix), xSz, ySz)
        return matrix

At the Python terminal, run the below commands.

```python
    import dbl_ptr_example as dp_ex
    x = np.zeros([5, 7], np.int32, order='C')
    y = dp_ex.callCLib(x)
    print(y)

The resulting output should look like this:

```python
    [[  0 100 200 300 400 500 600]
     [  1 101 201 301 401 501 601]
     [  2 102 202 302 402 502 602]
     [  3 103 203 303 403 503 603]
     [  4 104 204 304 404 504 604]]

