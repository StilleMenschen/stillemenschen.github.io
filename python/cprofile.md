# cProfile

```python
import cProfile
import pstats
import os


def enable_cprofile(filename):
    """
    Decorator for function profiling.
    """

    def wrapper(func):
        def profiled_func(*args, **kwargs):
            # Flag for enable profiling or disable.
            enable_profile = os.getenv("ENABLE_PROFILE")
            if enable_profile:
                profile = cProfile.Profile()
                profile.enable()
                result = func(*args, **kwargs)
                profile.disable()
                # Sort stat by internal time.
                sort_by = "tottime"
                ps = pstats.Stats(profile).sort_stats(sort_by)
                ps.print_stats()
                ps.dump_stats(filename)
            else:
                result = func(*args, **kwargs)
            return result

        return profiled_func

    return wrapper
```

```python
from profiling import enable_cprofile


def get_pattern(substring):
    pattern = [-1 for _ in substring]
    substring_length = len(substring)

    i, j = 1, 0
    while i < substring_length:
        if substring[i] == substring[j]:
            pattern[i] = j
            i += 1
            j += 1
        elif j > 0:
            j = pattern[j - 1] + 1
        else:
            i += 1
    return pattern


def does_match(string, substring, pattern):
    substring_length = len(substring)
    string_length = len(string)

    i, j = 0, 0

    while i + substring_length - j <= string_length:
        if string[i] == substring[j]:
            if j == substring_length - 1:
                return True
            i += 1
            j += 1
        elif j > 0:
            j = pattern[j - 1] + 1
        else:
            i += 1
    return False


def knuth_morris_pratt_algorithm(string, substring):
    if not len(string) or not len(substring) or len(string) < len(substring):
        return False
    pattern = get_pattern(substring)

    return does_match(string, substring, pattern)


@enable_cprofile('./algorithm.prof')
def main():
    print(knuth_morris_pratt_algorithm('alg normal algorithm is', 'algo'))
    print(knuth_morris_pratt_algorithm('abxabcabcaby', 'abcaby'))


if __name__ == '__main__':
    main()
```

```bash
export ENABLE_PROFILE=True
python3 algorithm.py
```

Last Modified 2022-02-14
