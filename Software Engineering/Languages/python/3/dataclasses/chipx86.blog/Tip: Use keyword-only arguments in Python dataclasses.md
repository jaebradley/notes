# [Tip: Use keyword-only arguments in Python dataclasses](https://chipx86.blog/2025/06/29/tip-use-keyword-only-arguments-in-python-dataclasses/)
* Always construct dataclasses with `@dataclass(kw_only=True)`
* Construct init with `self, *, a, b, c` instead of `self, a, b, c`
* `*` ensures that everything that follows must be passed as a keyword argument instead of a positional argument
* Reorder fields without breaking callers
* Allows subclasses to add required fields
* So if there are fields with default values, that manes that any field following it must also have a default including fields defined by a subclass
* Only supported in Python 3.10+
