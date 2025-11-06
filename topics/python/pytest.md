# Pytest

|Layer|What to test|Use DB?|Use mocks?|
|---|---|---|---|
|**Repository**|SQL, schema, CRUD|✅ SQLite or test DB|❌|
|**Service**|business rules|❌|✅ mock repo|
|**Controller/API**|HTTP wiring|❌|✅ mock service (and repo if direct)|

### 1. Repository tests → use SQLite (or real DB fixture)

You are testing:
- Table mapping (`__tablename__`, columns, relationships)
- Query filters, inserts, updates, deletes
- Transactions and rollbacks
```python
# tests/conftest.py
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from myapp.db import Base

@pytest.fixture(scope="session")
def engine():
    eng = create_engine("sqlite+pysqlite:///:memory:", future=True)
    Base.metadata.create_all(eng)
    return eng

@pytest.fixture
def dbsession(engine):
    # per-test transaction that rolls back
    connection = engine.connect()
    tx = connection.begin()
    Session = sessionmaker(bind=connection, future=True)
    session = Session()
    try:
        yield session
    finally:
        session.close()
        tx.rollback()
        connection.close()

# test_repo.py
def test_userrepo_crud(dbsession):
    repo = UserRepo(dbsession)
    u = repo.add_user("Ada")
    assert repo.get_user(u.id).name == "Ada"
```

### 2. Service tests → mock the repository

Goal: verify **business logic**, not SQL.
You are testing:
- Correct call to repo (`get_user`, `save_order`)
- Branching, validation, exceptions, retries
- Data transformation before/after repo
```python
from unittest.mock import Mock
import pytest

def test_greet_ok():
    repo = Mock()
    repo.get_user.return_value = type("U", (), {"name":"Ada"})
    svc = UserService(repo)
    assert svc.greet(1) == "Hi Ada"
    repo.get_user.assert_called_once_with(1)

def test_greet_notfound():
    repo = Mock(get_user=Mock(return_value=None))
    svc = UserService(repo)
    with pytest.raises(ValueError):
        svc.greet(2)
```

### 3. Controller / API tests → mock service or repo

Goal: verify **quest–response flow** (status codes, body, routing, DI).
You are testing:
- Correct HTTP response for given input
- Dependency injection override
- Parameter parsing and error handling
```python
from fastapi.testclient import TestClient
from myapp.api import app, get_service

def test_greet_endpoint():
    mock_svc = Mock()
    mock_svc.greet.return_value = "Hi Ada"
    app.dependency_overrides[get_service] = lambda: mock_svc
    client = TestClient(app)
    r = client.get("/greet/1")
    assert r.json() == {"msg":"Hi Ada"}
    app.dependency_overrides.clear()
```


### Patch, MonkeyPatch, Mock

When to use which
- Mock a **synchronous** collaborator: `Mock`
	- MagicMock implements magic methods (aka `__dunder__` methods)
- Mock an **async** collaborator: `AsyncMock`.
- Code **uses context managers/len/iter**: `MagicMock`.
- Replace imported global or env: `patch` or `monkeypatch`.
- Guard against API drift: use `autospec=True`.

```python
from unittest.mock import Mock, MagicMock, AsyncMock, patch, create_autospec
import pytest
import asyncio

# 1) Sync collaborator
def send(email_client, to, body):
    email_client.connect()
    email_client.send(to, body)
    email_client.disconnect()

def test_send_calls_in_order():
    client = Mock()
    send(client, "a@b", "hi")
    assert client.mock_calls == [
        patch.call.connect(),
        patch.call.send("a@b", "hi"),
        patch.call.disconnect(),
    ]

# 2) autospec catches wrong args
class Email:
    def send(self, to: str, body: str): ...
def test_autospec_signature():
    m = create_autospec(Email, instance=True)
    m.send("a@b", "hi")     # OK
    with pytest.raises(TypeError):
        m.send("only-one-arg")  # wrong arity detected

# 3) Async collaborator
async def fetch(client, uid):
    return await client.get(uid)

@pytest.mark.asyncio
async def test_asyncmock():
    client = Mock()
    client.get = AsyncMock(return_value={"id": 1})
    out = await fetch(client, 1)
    assert out == {"id": 1}
    client.get.assert_awaited_once_with(1)

# 4) Magic methods
def total_length(seq):
    return len(seq)

def test_magicmock_len():
    seq = MagicMock()
    seq.__len__.return_value = 10
    assert total_length(seq) == 10

# 5) patching import site
def compute_year():
    from mypkg.timeutil import now_utc
    return now_utc().year

def test_patch_import_site():
    import datetime as dt
    with patch("mypkg.timeutil.now_utc", return_value=dt.datetime(2000,1,1)):
        assert compute_year() == 2000

# 6) monkeypatch env and attribute
def test_monkeypatch(monkeypatch):
    monkeypatch.setenv("API_KEY", "t")
    import os
    assert os.getenv("API_KEY") == "t"
    import math
    monkeypatch.setattr(math, "sqrt", lambda x: 7)
    assert math.sqrt(9) == 7
```




```python
# tests/test_math.py
import pytest

def add(x, y): return x + y

@pytest.mark.parametrize("x,y,out", [(1,2,3),(0,0,0),(-1,1,0)])
def test_add(x, y, out):
    assert add(x, y) == out
```

```python
# tests/test_service_with_mock.py
from unittest.mock import Mock, AsyncMock, patch

def greet(repo):
    # repo.get_user returns {"name": "..."}
    return f"hi {repo.get_user(1)['name']}"

def test_greet_with_mock():
    repo = Mock()
    repo.get_user.return_value = {"name": "Ada"}
    assert greet(repo) == "hi Ada"

# Async example
async def fetch_user(client, uid):
    return await client.get(uid)  # must be awaited

import pytest
@pytest.mark.asyncio
async def test_fetch_user_with_asyncmock():
    client = Mock()
    client.get = AsyncMock(return_value={"id": 1})
    assert await fetch_user(client, 1) == {"id": 1}

# patching a global function with context manager
def compute():
    from myapp.util import now_utc
    return now_utc().year

def test_patch_with_unittest_patch():
    with patch("myapp.util.now_utc") as p:
        import datetime as dt
        p.return_value = dt.datetime(2000,1,1)
        assert compute() == 2000
```



```python
```