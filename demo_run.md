DEMO RUN — captured July 4


--- Planner ---
```json
{
  "function_name": "is_strong_password",
  "description": "Checks if a given password meets the strength criteria.",
  "parameters": [
    {
      "name": "password",
      "type": "string",
      "description": "The password string to evaluate."
    }
  ],
  "rules": [
    {
      "order": 1,
      "description": "The password must be at least 10 characters long."
    },
    {
      "order": 2,
      "description": "The password must contain at least one uppercase letter (A-Z)."
    },
    {
      "order": 3,
      "description": "The password must contain at least one lowercase letter (a-z)."
    },
    {
      "order": 4,
      "description": "The password must contain at least one digit (0-9)."
    },
    {
      "order": 5,
      "description": "The password must contain at least one special character from the following set: !@#$%^&*()-=_+|[]{}<>,.?/~"
    }
  ],
  "return_format": {
    "type": "boolean",
    "description": "Returns true if all strength criteria are met, false otherwise."
  },
  "examples": [
    {
      "input": {
        "password": "Password123!"
      },
      "output": true
    },
    {
      "input": {
        "password": "password123!"
      },
      "output": false
    },
    {
      "input": {
        "password": "Password!"
      },
      "output": false
    },
    {
      "input": {
        "password": "Pass123!"
      },
      "output": false
    },
    {
      "input": {
        "password": "Password1234567890"
      },
      "output": false
    },
    {
      "input": {
        "password": "P@$$wOrd123"
      },
      "output": true
    }
  ]
}
```

--- Coder ---
```python
import re

def is_strong_password(password: str) -> bool:
  """
  Checks if a given password meets the strength criteria.

  Args:
    password: The password string to evaluate.

  Returns:
    True if all strength criteria are met, false otherwise.
  """

  # Rule 1: The password must be at least 10 characters long.
  if len(password) < 10:
    return False

  # Rule 2: The password must contain at least one uppercase letter (A-Z).
  if not re.search(r"[A-Z]", password):
    return False

  # Rule 3: The password must contain at least one lowercase letter (a-z).
  if not re.search(r"[a-z]", password):
    return False

  # Rule 4: The password must contain at least one digit (0-9).
  if not re.search(r"[0-9]", password):
    return False

  # Rule 5: The password must contain at least one special character.
  if not re.search(r"[!@#$%^&*()\-=_+|\[\]{}<>,.?/~]", password):
    return False

  # If all rules are met, the password is strong.
  return True
```

--- TestWriter ---
```python
import pytest
from solution import is_strong_password

@pytest.mark.parametrize("password, expected", [
    ("Password123!", True),
    ("password123!", False),
    ("Password!", False),
    ("Pass123!", False),
    ("Password1234567890", False),
    ("P@$$wOrd123", True),
])
def test_is_strong_password_examples(password, expected):
    """
    Tests the is_strong_password function with the provided examples.
    """
    assert is_strong_password(password) == expected

def test_is_strong_password_empty():
    """
    Tests the is_strong_password function with an empty string.
    Rule 1: The password must be at least 10 characters long. (Fails)
    """
    assert is_strong_password("") == False

def test_is_strong_password_too_short_no_special_chars():
    """
    Tests a password that is too short but contains all other required character types.
    Rule 1: The password must be at least 10 characters long. (Fails)
    """
    assert is_strong_password("Abc1") == False

def test_is_strong_password_no_uppercase():
    """
    Tests a password that meets length and has other characters but no uppercase.
    Rule 2: The password must contain at least one uppercase letter (A-Z). (Fails)
    """
    assert is_strong_password("password123!@#") == False

def test_is_strong_password_no_lowercase():
    """
    Tests a password that meets length and has other characters but no lowercase.
    Rule 3: The password must contain at least one lowercase letter (a-z). (Fails)
    """
    assert is_strong_password("PASSWORD123!@#") == False

def test_is_strong_password_no_digit():
    """
    Tests a password that meets length and has other characters but no digit.
    Rule 4: The password must contain at least one digit (0-9). (Fails)
    """
    assert is_strong_password("Password!@#abc") == False

def test_is_strong_password_no_special_char():
    """
    Tests a password that meets length and has other characters but no special character.
    Rule 5: The password must contain at least one special character. (Fails)
    """
    assert is_strong_password("Password123abc") == False

def test_is_strong_password_exactly_10_chars_all_types():
    """
    Tests a password that is exactly 10 characters long and meets all other criteria.
    """
    assert is_strong_password("Abcdef123!") == True

def test_is_strong_password_long_with_all_types():
    """
    Tests a longer password that meets all criteria.
    """
    assert is_strong_password("ThisIsAVeryStrongPassword123!@#") == True

def test_is_strong_password_special_chars_from_set():
    """
    Tests a password with a special character that is explicitly in the allowed set.
    """
    assert is_strong_password("Password123.") == True

def test_is_strong_password_special_chars_not_from_set():
    """
    Tests a password with a special character NOT in the allowed set.
    Rule 5: The password must contain at least one special character from the following set: !@#$%^&*()-=_+|[]{}<>,.?/~ (Fails)
    """
    assert is_strong_password("Password123`") == False
```

--- Runner ---
All tests passed.


=== Reviewer Report (generated from scanner output) ===

Description: This code implements a password strength checker. It validates
that a password is at least 10 characters long and contains at least one
uppercase letter, one lowercase letter, one digit, and one special character
from a defined set. It returns True if all rules pass, False otherwise.

Issues:
- Critical: None. Secrets scan found no hardcoded credentials. Bandit found
  no security issues at any severity level.
- Warnings: None.
- Best Practices: The regex character class for special characters is
  hard-coded inside the function. For maintainability, consider extracting
  it as a module-level constant so the allowed set can be updated in one place.
- Quick Win: LGTM — code is safe to ship as a self-contained utility. Consider
  adding a small docstring example so downstream users see expected inputs
  at a glance.

Verdict: LGTM.
