---
title: "A Small Note on Backoffs"
date: 2023-08-24T18:16:49+02:00
draft: true
url: /2023/08/24/a-small-note-on-backoffs/
categories:
  - programming
  - maths
tags:
  - python
  - http
---

When sending data to are requesting data from an HTTP endpoint you expect
everything to go smoothly, don't you? You use your favorite library to emit the
request, you evaluate what came back...

## Backoff implementations

### httpx

httpx uses httpcore under the hood: [Blabla](https://github.com/encode/httpcore/blob/master/httpcore/_sync/connection.py)

```python
RETRIES_BACKOFF_FACTOR = 0.5  # 0s, 0.5s, 1s, 2s, 4s, etc.

# ...

def exponential_backoff(factor: float) -> Iterator[float]:
    """
    Generate a geometric sequence that has a ratio of 2 and starts with 0.

    For example:
    - `factor = 2`: `0, 2, 4, 8, 16, 32, 64, ...`
    - `factor = 3`: `0, 3, 6, 12, 24, 48, 96, ...`
    """
    yield 0
    for n in itertools.count():
        yield factor * 2**n


