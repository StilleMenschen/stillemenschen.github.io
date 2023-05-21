# Python 虚拟机


```python
>>> snippet = "for i in range(3): print(f'Output: {i}')"

>>> result = compile(snippet, "", "exec")

>>> result
<code object <module> at 0x7f8e7e6471e0, file "", line 1>

>>> exec(result)
Output: 0
Output: 1
Output: 2

>>> import dis
>>> dis.dis(result.co_code)
        0 RESUME                   0
        2 PUSH_NULL
        4 LOAD_NAME                0
        6 LOAD_CONST               0
        8 PRECALL                  1
        12 CALL                     1
        22 GET_ITER
>>   24 FOR_ITER                16 (to 58)
        26 STORE_NAME               1
        28 PUSH_NULL
        30 LOAD_NAME                2
        32 LOAD_CONST               1
        34 LOAD_NAME                1
        36 FORMAT_VALUE             0
        38 BUILD_STRING             2
        40 PRECALL                  1
        44 CALL                     1
        54 POP_TOP
        56 JUMP_BACKWARD           17 (to 24)
>>   58 LOAD_CONST               2
        60 RETURN_VALUE
```

更多参考[Python 虚拟机](https://smartkeyerror.com/Python-Virtual-Machine)

Last Modified 2023-05-21
