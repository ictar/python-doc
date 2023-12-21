原文：[Real-world match case](https://nedbatchelder.com/blog/202312/realworld_matchcase.html)

---

Python 3.10 为我们带来了结构模式匹配，更广为人知的名称是 **match/case**。乍一看，它像来自 C 或 JavaScript 的 switch 语句，但其实有很大不同。

您可以使用 match/case 来匹配特定的文字，类似于 switch 语句的工作方式，但它们的目的是匹配数据结构中的模式，而不仅仅是值。[PEP 636：结构模式匹配：教程](https://peps.python.org/pep-0636/)很好地解释了其机制，但感觉就像一个玩具示例。

这是一个现实世界中的使用示例：在工作中，我们安装了一个 GitHub 机器人作为 Webhook。当我们的某个存储库发生了些什么时，GitHub 会向我们的机器人发送 JSON 格式的有效负载。机器人必须检查解码后的有效负载来决定要做什么。

这些有效负载很复杂：它们是只包含 6 或 8 个键的字典，但它们嵌套极深，最终包含数百条数据。最初我们将它们分开以查看它们有什么键值，但是 match/case 使工作变得更加简单。

以下是一些代码，用于确定当我们收到“comment created（评论已创建）”事件时要执行的操作：

```py
# Check the structure of the payload:
match event:
    case {
        "issue": {"closed_at": closed},
        "comment": {"created_at": commented},
        } if closed == commented:
        # This is a "Close with comment" comment. Don't do anything for the
        # comment, because we'll also get a "pull request closed" event at
        # the same time, and it will do whatever we need.
        pass

    case {"sender": {"login": who}} if who == get_bot_username():
        # When the bot comments on a pull request, it causes an event, which
        # gets sent to webhooks, including us.  We don't have to do anything
        # for our own comment events.
        pass

    case {"issue": {"pull_request": _}}:
        # The comment is on a pull request. Process it.
        return process_pull_request_comment(event)
```

如果字典有一个“issue”键，值为一个带有“close_at”键的字典，并且还有一个“comment”键，值为一个带有“created_at”键的字典，并且字典中的这两个键值相等，则第一种情况匹配。在没有 match/case 的情况下要写出这种条件，代码会更加冗长和混乱。

第二种情况检查 event 以查看机器人是否是 event 的发起者。如果用不同的方式来写这个就不那么难了，但是 match/case 让代码更漂亮。

这正是 match/case 所擅长的：检查数据结构中的模式。

看看生成的字节码也很有趣。对于第一种情况，它看起来像这样：

```
  2           0 LOAD_GLOBAL              0 (event)

  3           2 MATCH_MAPPING
              4 POP_JUMP_IF_FALSE       67 (to 134)
              6 GET_LEN
              8 LOAD_CONST               1 (2)
             10 COMPARE_OP               5 (>=)
             12 POP_JUMP_IF_FALSE       67 (to 134)

  4          14 NOP

  5          16 NOP

  3          18 LOAD_CONST               8 (('issue', 'comment'))
             20 MATCH_KEYS
             22 POP_JUMP_IF_FALSE       65 (to 130)
             24 DUP_TOP
             26 LOAD_CONST               4 (0)
             28 BINARY_SUBSCR

  4          30 MATCH_MAPPING
             32 POP_JUMP_IF_FALSE       64 (to 128)
             34 GET_LEN
             36 LOAD_CONST               5 (1)
             38 COMPARE_OP               5 (>=)
             40 POP_JUMP_IF_FALSE       64 (to 128)
             42 LOAD_CONST               9 (('closed_at',))
             44 MATCH_KEYS
             46 POP_JUMP_IF_FALSE       62 (to 124)
             48 DUP_TOP
             50 LOAD_CONST               4 (0)
             52 BINARY_SUBSCR
             54 ROT_N                    7
             56 POP_TOP
             58 POP_TOP
             60 POP_TOP
             62 DUP_TOP
             64 LOAD_CONST               5 (1)
             66 BINARY_SUBSCR

  5          68 MATCH_MAPPING
             70 POP_JUMP_IF_FALSE       63 (to 126)
             72 GET_LEN
             74 LOAD_CONST               5 (1)
             76 COMPARE_OP               5 (>=)
             78 POP_JUMP_IF_FALSE       63 (to 126)
             80 LOAD_CONST              10 (('created_at',))
             82 MATCH_KEYS
             84 POP_JUMP_IF_FALSE       61 (to 122)
             86 DUP_TOP
             88 LOAD_CONST               4 (0)
             90 BINARY_SUBSCR
             92 ROT_N                    8
             94 POP_TOP
             96 POP_TOP
             98 POP_TOP
            100 POP_TOP
            102 POP_TOP
            104 POP_TOP
            106 STORE_FAST               0 (closed)
            108 STORE_FAST               1 (commented)

  6         110 LOAD_FAST                0 (closed)
            112 LOAD_FAST                1 (commented)
            114 COMPARE_OP               2 (==)
            116 POP_JUMP_IF_FALSE       70 (to 140)

 10         118 LOAD_CONST               0 (None)
            120 RETURN_VALUE

  3     >>  122 POP_TOP
        >>  124 POP_TOP
        >>  126 POP_TOP
        >>  128 POP_TOP
        >>  130 POP_TOP
            132 POP_TOP
        >>  134 POP_TOP
            136 LOAD_CONST               0 (None)
            138 RETURN_VALUE

  6     >>  140 LOAD_CONST               0 (None)
            142 RETURN_VALUE
```

虽然很长，但您可以大致了解它在做什么：检查该值是否是包含至少两个键（字节码 2-12）的映射（字典），然后检查它是否包含我们将要检查的两个特定键（ 18-22）。查看第一个键的值，检查它是否是一个至少有一个键（24-40）的字典，等等。

手写这些类型的检查可能会导致字节码更短。例如，我已经知道 event 的值是一个字典，因为这是 GitHub API 向我承诺的，因此无需每次都显式检查它。但 Python 代码会更加曲折且更难执行正确。我最初对 match/case 持怀疑态度，但这个例子显示了它的一个明显的优势。