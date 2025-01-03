# Test Command in Linux

`test` is command, which is common in bash script, with the format of `[]`.
## File Property
- `-f FILE`: 检查文件是否是普通文件。
- `-d FILE`: 检查文件是否是目录。
- `-e FILE`: 检查文件是否存在。
- `-r FILE`: 检查文件是否可读。
- `-w FILE`: 检查文件是否可写。
- `-x FILE`: 检查文件是否可执行。
## String Comparison
- `=`: 比较两个字符串是否相等。
- `!=`: 比较两个字符串是否不相等。
- `-z STRING`: 检查字符串是否为空。
- `-n STRING`: 检查字符串是否非空。
## Number Comparison
- `-eq`: 等于（equal）。
- `-ne`: 不等于（not equal）。
- `-lt`: 小于（less than）。
- `-le`: 小于等于（less than or equal）。
- `-gt`: 大于（greater than）。
- `-ge`: 大于等于（greater than or equal）。
## Logic
- `-a`: 逻辑与 (and)。
- `-o`: 逻辑或 (or)。
- `!`: 逻辑非 (not)。
## Notice
- `[ condition ]` 是 `test` 的简写，但必须确保方括号两边有空格。
- 推荐使用 `[[ ... ]]` 替代 `[`，因为 `[[` 提供了更强大的功能，比如支持正则表达式匹配。