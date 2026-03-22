# 사용자 지정 MCP 서버 구축

> ⚠️ **이 내용은 선택 사항입니다.** 미리 구축된 MCP 서버(GitHub, 파일 시스템, Context7)만으로도 Copilot CLI를 충분히 활용할 수 있습니다. 이 가이드는 Copilot을 사용자 지정 내부 API에 연결하려는 개발자를 위한 것입니다. 자세한 내용은 [MCP 초보자 과정](https://github.com/microsoft/mcp-for-beginners)을 참조하세요.

>
> **필수 조건:**
> - Python에 대한 기본적인 이해
> - `async`/`await` 패턴에 대한 이해
> - 시스템에 `pip` 설치 (개발 컨테이너에 포함됨)
>
> **[← 6장: MCP 서버로 돌아가기](README.md)**
---

Copilot을 자체 API에 연결하고 싶으신가요? 이 강좌에서 사용해 온 도서 앱 프로젝트와 연동하여 도서 정보를 조회하는 간단한 MCP 서버를 Python으로 구축하는 방법을 소개합니다.

## 프로젝트 설정

```bash
mkdir book-lookup-mcp-server
cd book-lookup-mcp-server
pip install mcp
```

> 💡 **`mcp` 패키지란 무엇인가요?** MCP 서버 구축을 위한 공식 Python SDK입니다. 프로토콜 관련 세부 사항은 패키지에서 처리하므로 개발자는 도구 개발에 집중할 수 있습니다.

## 서버 구현

`server.py`라는 파일을 생성하세요.

```python
# server.py
import json
from mcp.server.fastmcp import FastMCP

# Create the MCP server
mcp = FastMCP("book-lookup")

# Sample book database (in a real server, this could query an API or database)
BOOKS_DB = {
    "978-0-547-92822-7": {
        "title": "The Hobbit",
        "author": "J.R.R. Tolkien",
        "year": 1937,
        "genre": "Fantasy",
    },
    "978-0-451-52493-5": {
        "title": "1984",
        "author": "George Orwell",
        "year": 1949,
        "genre": "Dystopian Fiction",
    },
    "978-0-441-17271-9": {
        "title": "Dune",
        "author": "Frank Herbert",
        "year": 1965,
        "genre": "Science Fiction",
    },
}


@mcp.tool()
def lookup_book(isbn: str) -> str:
    """Look up a book by its ISBN and return title, author, year, and genre."""
    book = BOOKS_DB.get(isbn)
    if book:
        return json.dumps(book, indent=2)
    return f"No book found with ISBN: {isbn}"


@mcp.tool()
def search_books(query: str) -> str:
    """Search for books by title or author. Returns all matching results."""
    query_lower = query.lower()
    results = [
        {**book, "isbn": isbn}
        for isbn, book in BOOKS_DB.items()
        if query_lower in book["title"].lower()
        or query_lower in book["author"].lower()
    ]
    if results:
        return json.dumps(results, indent=2)
    return f"No books found matching: {query}"


@mcp.tool()
def list_all_books() -> str:
    """List all books in the database with their ISBNs."""
    books_list = [
        {"isbn": isbn, "title": book["title"], "author": book["author"]}
        for isbn, book in BOOKS_DB.items()
    ]
    return json.dumps(books_list, indent=2)


if __name__ == "__main__":
    mcp.run()
```

**여기서 무슨 일이 일어나고 있나요?**

| 파트 | 기능 |
|------|-------------|
| `FastMCP("book-lookup")` | "book-lookup"이라는 이름의 서버를 생성합니다. |
| `@mcp.tool()` | Copilot이 호출할 수 있는 도구로 함수를 등록합니다. |
| 타입 힌트 + 독스트링 | 각 도구의 기능과 필요한 매개변수를 Copilot에 알려줍니다. |
| `mcp.run()` | 서버를 시작하고 요청을 수신합니다. |

> 💡 **데코레이터를 사용하는 이유?** `@mcp.tool()` 데코레이터만 있으면 됩니다. MCP SDK는 함수의 이름, 타입 힌트, 독스트링을 자동으로 읽어 도구 스키마를 생성합니다. 수동으로 JSON 스키마를 작성할 필요가 없습니다!

## 설정

`~/.copilot/mcp-config.json` 파일에 다음을 추가하세요.

```json
{
  "mcpServers": {
    "book-lookup": {
      "type": "local",
      "command": "python3",
      "args": ["./book-lookup-mcp-server/server.py"],
      "tools": ["*"]
    }
  }
}
```

## Usage

```bash
copilot

> Look up the book with ISBN 978-0-547-92822-7

{
  "title": "The Hobbit",
  "author": "J.R.R. Tolkien",
  "year": 1937,
  "genre": "Fantasy"
}

> Search for books by Orwell

[
  {
    "title": "1984",
    "author": "George Orwell",
    "year": 1949,
    "genre": "Dystopian Fiction",
    "isbn": "978-0-451-52493-5"
  }
]

> List all available books

[Shows all books in the database with ISBNs]
```

## 다음 단계

기본 서버 구축이 완료되면 다음 작업을 수행할 수 있습니다.

1. **도구 추가** - 각 `@mcp.tool()` 함수는 Copilot에서 호출할 수 있는 도구가 됩니다.
2. **실제 API 연결** - 모의 `BOOKS_DB`를 실제 API 호출 또는 데이터베이스 쿼리로 교체합니다.
3. **인증 추가** - API 키와 토큰을 안전하게 관리합니다.
4. **서버 공유** - 다른 사용자가 `pip`으로 설치할 수 있도록 PyPI에 게시합니다.

## 리소스

- [MCP Python SDK](https://github.com/modelcontextprotocol/python-sdk)
- [MCP TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk)
- [Example MCP Servers](https://github.com/modelcontextprotocol/servers)
- [MCP for Beginners Course](https://github.com/microsoft/mcp-for-beginners)

---

**[← Back to Chapter 06: MCP Servers](README.md)**
