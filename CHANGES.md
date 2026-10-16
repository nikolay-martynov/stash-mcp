# Local Modifications

This is a personal fork of [stash-mcp](https://github.com/schwarztim/stash-mcp) with custom modifications.

See git history for details (`git diff upstream/main`).

## Changes

- Add `get_branch_diff` and `get_commit_diff` tools to inspect diffs between branches/refs and individual commits, not just pull requests
- Add chunked output for PR diffs (`fileOffset`/`fileLimit` pagination) to avoid overwhelming LLM context with large diffs
- Condense activity API responses into compact, LLM-friendly output: users as `name (displayName)`, epoch dates as ISO, inline comment anchors with diff context, rescoped commits with short hashes
- Wire `get_activities` pagination: expose `limit`/`start` in the tool schema and forward them to the Bitbucket API (previously the response carried `nextPageStart` but the request ignored it, so only the first page was reachable)
- Wire `get_comments` pagination: expose `limit`/`start` and forward them; the response is now an object with page metadata (`size`/`limit`/`isLastPage`/`start`/`nextPageStart`) plus `comments` (was a bare array). Previously it fetched only the first page of activities (default 25) with no way to page, silently dropping comments beyond it - on a 99-activity PR it returned 25 of 73 comments.
- Wire `get_reviews` pagination: same treatment as `get_comments` (page metadata + `reviews`); previously only the first page of activities was filtered.
- Add `start` to `browse_repository` (it already exposed `limit`), so directories larger than `limit` can be paged.