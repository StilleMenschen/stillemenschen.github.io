# pytest

## 命令行选项

<style>
table th:first-of-type {
    width: 30%;
}
</style>

| 选项                 | 说明                                                                                                                                               |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| -v, --verbose        | 展示更详细的输出信息                                                                                                                               |
| -q, --quiet          | 精简输出信息                                                                                                                                       |
| --lf, --last-failed  | 仅运行上次失败的测试                                                                                                                               |
| --ff, --failed-first | 先运行上次失败的测试再运行上次通过的测试                                                                                                           |
| -x, --exitfirst      | 遇到一次失败后停止执行测试                                                                                                                         |
| --maxfail=NUM        | 遇到第 NUM 次测试失败后停止执行测试                                                                                                                |
| -l, --showlocals     | 显示本地变量的值                                                                                                                                   |
| -r char              | 列出指定类型的摘要信息，可用的枚举包括：<br>(f)ailed, (E)rror, (s)kipped, (x)failed, (X)passed, (p)assed, (P)assed, (a) 除了 passed 的, (A) 所有的 |

## 跳过测试

直接跳过

```python
import pytest


@pytest.mark.skip(reason="no way of currently testing this")
def test_the_unknown():
    pass
```

按条件跳过

```python
import pytest


def test_function():
    if not valid_config():
        pytest.skip("unsupported configuration")
```

```python
import sys
import pytest

if not sys.platform.startswith("win"):
    pytest.skip("skipping windows-only tests", allow_module_level=True)
```

```python
import sys
import pytest


@pytest.mark.skipif(sys.version_info < (3, 7), reason="requires python3.7 or higher")
def test_function():
    pass
```

```python
# content of test_mymodule.py
import mymodule
import pytest


minversion = pytest.mark.skipif(
    mymodule.__versioninfo__ < (1, 1), reason="at least mymodule-1.1 required"
)


@minversion
def test_function():
    pass
```

导入模块失败则跳过

```python
docutils = pytest.importorskip("docutils")
```

## 依赖注入

```python
import pytest


class Fruit:
    def __init__(self, name):
        self.name = name
        self.cubed = False

    def cube(self):
        self.cubed = True


class FruitSalad:
    def __init__(self, *fruit_bowl):
        self.fruit = fruit_bowl
        self._cube_fruit()

    def _cube_fruit(self):
        for fruit in self.fruit:
            fruit.cube()


# Arrange
@pytest.fixture
def fruit_bowl():
    return [Fruit("apple"), Fruit("banana")]


def test_fruit_salad(fruit_bowl):
    # Act
    fruit_salad = FruitSalad(*fruit_bowl)

    # Assert
    assert all(fruit.cubed for fruit in fruit_salad.fruit)
```

```python
# contents of test_append.py
import pytest


# Arrange
@pytest.fixture
def first_entry():
    return "a"


# Arrange
@pytest.fixture
def second_entry():
    return 2


# Arrange
@pytest.fixture
def order(first_entry, second_entry):
    return [first_entry, second_entry]


# Arrange
@pytest.fixture
def expected_list():
    return ["a", 2, 3.0]


def test_string(order, expected_list):
    # Act
    order.append(3.0)

    # Assert
    assert order == expected_list
```

内外部类使用

```python
import pytest


@pytest.fixture
def order():
    return []


@pytest.fixture
def outer(order, inner):
    order.append("outer")


class TestOne:
    @pytest.fixture
    def inner(self, order):
        order.append("one")

    def test_order(self, order, outer):
        assert order == ["one", "outer"]


class TestTwo:
    @pytest.fixture
    def inner(self, order):
        order.append("two")

    def test_order(self, order, outer):
        assert order == ["two", "outer"]
```

自动使用

```python
# contents of test_append.py
import pytest


@pytest.fixture
def first_entry():
    return "a"


@pytest.fixture
def order(first_entry):
    return []


@pytest.fixture(autouse=True)
def append_first(order, first_entry):
    return order.append(first_entry)


def test_string_only(order, first_entry):
    assert order == [first_entry]


def test_string_and_int(order, first_entry):
    order.append(2)
    assert order == [first_entry, 2]
```

## 捕获异常

```python
import pytest


def test_recursion_depth():
    with pytest.raises(RuntimeError) as excinfo:
        def f():
            f()

        f()
    assert "maximum recursion" in str(excinfo.value)
```

预测失败原因

```python
import pytest


def f():
    a = [0, 1, 3]
    a[5] = 9


@pytest.mark.xfail(raises=IndexError)
def test_f():
    f()
```

## 参数化测试

```python
import pytest


@pytest.mark.parametrize("test_input,expected", [("3+5", 8), ("2+4", 6), ("6*9", 42)])
def test_eval(test_input, expected):
    assert eval(test_input) == expected
```

```python
import pytest


@pytest.mark.parametrize("n,expected", [(1, 2), (3, 4)])
class TestClass:
    def test_simple_case(self, n, expected):
        assert n + 1 == expected

    def test_weird_simple_case(self, n, expected):
        assert (n * 1) + 1 == expected


@pytest.mark.parametrize(
    "test_input,expected",
    [("3+5", 8), ("2+4", 6), pytest.param("6*9", 42, marks=pytest.mark.xfail)],
)
def test_eval(test_input, expected):
    assert eval(test_input) == expected
```

命令行参数化

```python
# content of conftest.py


def pytest_addoption(parser):
    parser.addoption(
        "--stringinput",
        action="append",
        default=[],
        help="list of stringinputs to pass to test functions",
    )


def pytest_generate_tests(metafunc):
    if "stringinput" in metafunc.fixturenames:
        metafunc.parametrize("stringinput", metafunc.config.getoption("stringinput"))
```

```python
# content of test_strings.py


def test_valid_string(stringinput):
    assert stringinput.isalpha()
```

```bash
pytest -q --stringinput="hello" --stringinput="world"  --stringinput="123" test_strings.py
```

## 临时目录

临时目录仅在执行用例时有效

```python
CONTENT = "content"


def test_create_file(tmp_path):
    d = tmp_path / "sub"
    d.mkdir()
    p = d / "hello.txt"
    p.write_text(CONTENT)
    assert p.read_text() == CONTENT
    assert len(list(tmp_path.iterdir())) == 1
    assert 0
```

## 模拟数据

模拟属性

```python
# contents of test_app.py, a simple test for our API retrieval
# import requests for the purposes of monkeypatching
import requests


# our app.py that includes the get_json() function
# this is the previous code block example
def get_json(url):
    """Takes a URL, and returns the JSON."""
    r = requests.get(url)
    return r.json()


# custom class to be the mock return value
# will override the requests.Response returned from requests.get
class MockResponse:

    # mock json() method always returns a specific testing dictionary
    @staticmethod
    def json():
        return {"mock_key": "mock_response"}


def test_get_json(monkeypatch):
    # Any arguments may be passed and mock_get() will always return our
    # mocked object, which only has the .json() method.
    def mock_get(*args, **kwargs):
        return MockResponse()

    # apply the monkeypatch for requests.get to mock_get
    monkeypatch.setattr(requests, "get", mock_get)

    # app.get_json, which contains requests.get, uses the monkeypatch
    result = get_json("https://fakeurl")
    assert result["mock_key"] == "mock_response"
```

模拟环境变量

```python
# contents of our test file e.g. test_code.py
import pytest
import os


def get_os_user_lower():
    """Simple retrieval function.
    Returns lowercase USER or raises OSError."""
    username = os.getenv("USER")

    if username is None:
        raise OSError("USER environment is not set.")

    return username.lower()


@pytest.fixture
def mock_env_user(monkeypatch):
    monkeypatch.setenv("USER", "TestingUser")


@pytest.fixture
def mock_env_missing(monkeypatch):
    monkeypatch.delenv("USER", raising=False)


# notice the tests reference the fixtures for mocks
def test_upper_to_lower(mock_env_user):
    assert get_os_user_lower() == "testinguser"


def test_raise_exception(mock_env_missing):
    with pytest.raises(OSError):
        _ = get_os_user_lower()
```

模拟字典键值

```python
# contents of test_app.py
import pytest

DEFAULT_CONFIG = {"user": "user1", "database": "db1"}


def create_connection_string(config=None):
    """Creates a connection string from input or defaults."""
    config = config or DEFAULT_CONFIG
    return f"User Id={config['user']}; Location={config['database']};"


# all of the mocks are moved into separated fixtures
@pytest.fixture
def mock_test_user(monkeypatch):
    """Set the DEFAULT_CONFIG user to test_user."""
    monkeypatch.setitem(DEFAULT_CONFIG, "user", "test_user")


@pytest.fixture
def mock_test_database(monkeypatch):
    """Set the DEFAULT_CONFIG database to test_db."""
    monkeypatch.setitem(DEFAULT_CONFIG, "database", "test_db")


@pytest.fixture
def mock_missing_default_user(monkeypatch):
    """Remove the user key from DEFAULT_CONFIG"""
    monkeypatch.delitem(DEFAULT_CONFIG, "user", raising=False)


# tests reference only the fixture mocks that are needed
def test_connection(mock_test_user, mock_test_database):
    expected = "User Id=test_user; Location=test_db;"

    result = create_connection_string()
    assert result == expected


def test_missing_user(mock_missing_default_user):
    with pytest.raises(KeyError):
        _ = create_connection_string()
```

## 执行前后钩子

模块执行前后调用

```python
def setup_module(module):
    """ setup any state specific to the execution of the given module."""


def teardown_module(module):
    """teardown any state that was previously setup with a setup_module method."""
```

测试类执行前后调用

```python
@classmethod
def setup_class(cls):
    """setup any state specific to the execution of the given class (which
    usually contains tests).
    """


@classmethod
def teardown_class(cls):
    """teardown any state that was previously setup with a call to setup_class."""
```

测试类中的方法执行前后调用

```python
def setup_method(self, method):
    """setup any state tied to the execution of the given method in a
    class.  setup_method is invoked for every test method of a class.
    """


def teardown_method(self, method):
    """teardown any state that was previously setup with a setup_method call."""
```

模块内的方法执行前后调用（非测试类中的方法）

```python
def setup_function(function):
    """setup any state tied to the execution of the given function.
    Invoked for every test function in the module.
    """


def teardown_function(function):
    """teardown any state that was previously setup with a setup_function call."""
```

Last Modified 2022-03-08
