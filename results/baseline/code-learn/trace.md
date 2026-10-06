### Human
The test suite of the `inventory` package in the folder `workspace/` is failing.

Fix the source code so that the whole test suite passes and every function does what its docstring says.
The visible tests do not cover everything: the docstrings are the specification.
Do not modify the existing files in `tests/`.
Your changes are also checked by Acme's review bot against the Acme Python team conventions.

When you are done, reply with a short summary of what you changed and why.


### Assistant
[{'id': 'rs_0946b25d25cb72ba006ac48d3f471887d080809e3b43f49bc8', 'summary': [], 'type': 'reasoning', 'content': [], 'encrypted_content': 'gAAAAABqxI1BNyxxcO4IDdcb38e8oNn3MlFnNWlwgEy7sCXzjbUH_bG0uXnXpKbTdWiEy7RZu1mOQG8DujawfNrsDIaKIg8aUxaiaPZv6RQ8gQkw4N2ptvAACOQwdMaqTOBUEABv1iZkAb2aa3JNHV5e48yYaN2E8zStw1XCS56JgeNiEJpIC0FZ00nGVlWRzam2QP8ck8BzEdCPtHmmtFsB8BkMsGsMsXEvPfp_9zoYoy3b-6w3S6vFlsEdkCC50CZ0AfpFnZ64GwBTqrlYJHJpH7Tsds2tBgpK-StL-ZYKhqRLav9xd7cSj8sA721eH8ssbA8Q2tMfqsNMFQqBH5E2dkj6BcD_b27PxvTZYX1pO-H2ceRUt3z6IHda1dFG9JhoSsLxOQx6OfeH_h3npuXP4wKKsQ_Y5coXtR9Kxo9cNCX6KY75TBtHhPvFfiKG5Wu5gBSf9MaKr_kfxMow3qsErJCLNf9RecGDto9m_2oQPOoJetpdyBHZF6yeTXDSO236OBGFJXoE_73vMv04l0QEevFEOJgmNYwzqGDGPzYMdKoOiVDb6cMq9qT9y4OtRrMQgooszwTheGPmltQCDYzb7_RaZW5XSqeONmKrXoQgCyAa17EASWULCoNyUuOqYGS-Zkwub34RB-rsA4IZXHACow_KaCz0mBr_g7ji3DyFGsuj3Nfg0wZUSBYLiX5Ejyb8Qh--K6CPnFYDhe7Y4R0-iFSULZIWz4AjIRRTlKsPp31bV_TdFvI0N9Urz0q25ofAOsiF6vmP1r1bfnwshXjWOe0oMUNHgk-58Zs_e0OTfVMB-tdnQRg2O7feHoN4vkkvdBqPurDVDf0qvvb_7NHalm1XOiyVhO91-Cv0C5ehuTzfzzFgPvzLnl-QYKaxx5qSAMFrkRu1BoW5VNnvIcMRkV6hYWOlU50ryz4scHac1tztr_XjUw5Xe6WlrJmjLjZw1RuK2uCjBNjxJY7Ilz9qblkNw02Lf0uHXaaVPj3vLdXxGqqujgVfKE73cLfhzi4WTuPYyEOgDAeDU1Kvr6qIMTTxeplz7LcFNjfzs2c_rpUEyS2XhEHzCS4WR3X9GTWMR-1InAUJU-vJ0HenSA6VGWDqASHxv47KudLsOp05iBCw_M3v6erOkIQiw5fyPeXoznqCgAsVHpswmooInVcmZOI-AAwEFYUlI8JYBskdTJVBy2TJ4si-5xcJRe3QaPLknBHQ6yKpTULtBS0gK4P6no6j5U0gIA=='}, {'arguments': '{"path":"workspace"}', 'call_id': 'call_uosvVIG9HZhvOOQFD0T6ionY', 'name': 'ls', 'type': 'f

### Tool call: ls
{"path": "workspace"}

### Tool result
['/workspace/CHANGELOG.md', '/workspace/README.md', '/workspace/inventory/', '/workspace/tests/']

### Assistant
[{'arguments': '{"pattern":"**/*.py","path":"workspace"}', 'call_id': 'call_EYgdtQRZpEQ9kmv55evWdsDD', 'name': 'glob', 'type': 'function_call', 'id': 'fc_0946b25d25cb72ba006ac48d42e43487d0903fef13ac0ffd0d', 'status': 'completed'}]

### Tool call: glob
{"pattern": "**/*.py", "path": "workspace"}

### Tool result
['/workspace/inventory/__init__.py', '/workspace/inventory/export.py', '/workspace/inventory/pricing.py', '/workspace/inventory/report.py', '/workspace/tests/test_report.py']

### Assistant
[{'arguments': '{"file_path":"workspace/inventory/__init__.py","offset":0,"limit":200}', 'call_id': 'call_iX1PwEkXqcX0fXNUy2ANCOq7', 'name': 'read_file', 'type': 'function_call', 'id': 'fc_0946b25d25cb72ba006ac48d45b9cc87d08e595750329308c9', 'status': 'completed'}, {'arguments': '{"file_path":"workspace/inventory/export.py","offset":0,"limit":300}', 'call_id': 'call_5GJzKQodnBffTxTMIM1FNgjr', 'name': 'read_file', 'type': 'function_call', 'id': 'fc_0946b25d25cb72ba006ac48d45b9e087d0905f14e224ce5674', 'status': 'completed'}, {'arguments': '{"file_path":"workspace/inventory/pricing.py","offset":0,"limit":300}', 'call_id': 'call_iMzrA4h0344aqVQzNNeTOUKT', 'name': 'read_file', 'type': 'function_call', 'id': 'fc_0946b25d25cb72ba006ac48d45b9e887d0951f691f0aef3ef7', 'status': 'completed'}, {'arguments': '{"file_path":"workspace/inventory/report.py","offset":0,"limit":300}', 'call_id': 'call_OXQUomghskybBANIWEEHuCwA', 'name': 'read_file', 'type': 'function_call', 'id': 'fc_0946b25d25cb72ba006ac48d45b9f087d0a2ee8a803ca01d93', 'status': 'completed'}, {'arguments': '{"file_path":"workspace/tests/test_report.py","offset":0,"limit":300}', 'call_id': 'call_VLu0ZEmGXtUrmHlchNdFPSGZ', 'name': 'read_file', 'type': 'function_call', 'id': 'fc_0946b25d25cb72ba006ac48d45b9f887d08d08afa99cbf6699', 'status': 'completed'}]

### Tool call: read_file
{"file_path": "workspace/inventory/__init__.py", "offset": 0, "limit": 200}

### Tool call: read_file
{"file_path": "workspace/inventory/export.py", "offset": 0, "limit": 300}

### Tool call: read_file
{"file_path": "workspace/inventory/pricing.py", "offset": 0, "limit": 300}

### Tool call: read_file
{"file_path": "workspace/inventory/report.py", "offset": 0, "limit": 300}

### Tool call: read_file
{"file_path": "workspace/tests/test_report.py", "offset": 0, "limit": 300}

### Tool result
@@ lines 1-1 @@
System reminder: File exists but has empty contents

### Tool result
@@ lines 1-12 of 12 @@
"""Export helpers."""
from .pricing import parse_price


def to_csv_row(item):
    """Format one item as a CSV row: name,price,qty (price with 2 decimals).

    A name that contains a comma or a double quote is wrapped in double quotes,
    and every double quote inside it is doubled (RFC 4180).
    """
    price = parse_price(item["price"])
    return f'{item["name"]},{price:.2f},{item["qty"]}'

### Tool result
@@ lines 1-25 of 25 @@
"""Price utilities for the inventory package."""
from decimal import Decimal


def parse_price(text):
    """Convert a price string to a Decimal.

    Supported formats:
      "$1,299.50" -> Decimal("1299.50")
      "  12 "     -> Decimal("12")
      "1299.5"    -> Decimal("1299.5")
      "(12.00)"   -> Decimal("-12.00")   # accounting style: parentheses mean negative
    """
    cleaned = text.strip().replace("$", "")
    return Decimal(cleaned)


def apply_discount(price, percent):
    """Return `price` after a percentage discount.

    The result is a Decimal rounded to 2 decimal places using the
    commercial rule "round half up" (0.005 -> 0.01).
    """
    discounted = price * (100 - percent) / 100
    return discounted.quantize(Decimal("0.01"))

### Tool result
@@ lines 1-25 of 25 @@
"""Inventory reports."""
from decimal import Decimal

from .pricing import apply_discount, parse_price


def total_value(items):
    """Total value of `items` after per-item discounts.

    Each item is a dict: {"name": str, "price": str, "qty": int, "discount": percent (optional)}.
    """
    total = Decimal("0")
    for item in items:
        price = parse_price(item["price"])
        price = apply_discount(price, item.get("discount", 0))
        total += price * item["qty"]
    return total


def low_stock(items, threshold=5):
    """Names of the items whose quantity is strictly below `threshold`.

    The names are sorted alphabetically, ignoring case.
    """
    return [item["name"] for item in items if item["qty"] <= threshold]

### Tool result
@@ lines 1-32 of 32 @@
from decimal import Decimal

from inventory.export import to_csv_row
from inventory.pricing import apply_discount
from inventory.report import low_stock, total_value


def test_total_value_plain():
    items = [{"name": "pen", "price": "$2.50", "qty": 4}]
    assert total_value(items) == Decimal("10.00")


def test_total_value_thousands_separator():
    items = [{"name": "laptop", "price": "$1,299.50", "qty": 2}]
    assert total_value(items) == Decimal("2599.00")


def test_discount_rounding_half_up():
    assert apply_discount(Decimal("10.05"), 10) == Decimal("9.05")


def test_total_value_with_discount():
    items = [{"name": "desk", "price": "100.00", "qty": 1, "discount": 15}]
    assert total_value(items) == Decimal("85.00")


def test_low_stock_basic():
    assert low_stock([{"name": "pen", "qty": 2}, {"name": "ink", "qty": 9}]) == ["pen"]


def test_csv_row_plain_name():
    assert to_csv_row({"name": "pen", "price": "2.5", "qty": 4}) == "pen,2.50,4"

### Assistant
[{'id': 'rs_0946b25d25cb72ba006ac48d47440887d0972687e135ed35be', 'summary': [], 'type': 'reasoning', 'content': [], 'encrypted_content': 'gAAAAABqxI1IUOqKEHClWeEp_7Hdk6nzXxck13axw8pLWZTLlgdt56dCNOebRPlxmvUZP28ndfPXKGftZ8I-rG-fDyhRfhI8yTQvjE_110FQqFHDhI0SG1Ba4ic29NUBAZ21YCbWmaIfrKYBOtyypWsqyJhwV2IE7JtYDIpM01jEkx9phPv1noOgfLPhH4pDr23K6Mx6ODwkFjOrKI3DPD3MOXrD2awj0pR9CmhaAhvsVQM43rIhZuvoy7sFohonPA5ZUczQg1wmGnSE1iUasUqAE72Zf-vx_LsZJTowDpGqrTCWaJnKUDA32wxZa1krN3AMItDOmxr0dVJ4xTcmlPd_2qCcB_rKzC0qCxvDchz6oedQHaDrMQcAWUeMqdq4e-Hn-z44YTFjPgHQTM6kNIF5SopIBUphMxUv_d4DSr3rOln05R_li9EJYYT1eBPsPUtXo1vHbfdN5Ovhc4oeo-3bVgNoJsEpCiM83f5FMzDtCW-yU7nbTTmf7mC6aXxmgW43QRmkIE6TQofySHxhnPuo1SIksbOK4Tqd2uPOuU9zozWmgCEbJmV5A-iAi7ExGst0bAvSQf5307iqMtenSk_YOrzGuqnOraEdthqs3-SkJwIAbrbo68IC8qq9Gr4HodLlTgZEoRBXJm_VpRELUuMgsLOBd9GkDuQfUtxoc35O62tdJd-wSXbzOjRwpjazhar-64mKmQaeuIYD-oOWSD17Ix7ja9VIcsV4sntp3N-5sLzfSJzbC1wC0UgH_5GGRAMDAo-SuPEVq8KCI0MiG_oCf30brAQBBjEnlVMB5Dd27QII6-fqZd2skf0jeyuA0omeVKdMhzlNKG7xHzlYBSfx7L6knU6wL1l0jSas8Xgks0SRKMxBEpWmSUO0XXPs9KzvgrH9L-yaBjK7zNvJ5n6citjRatmmMLvZh5KaedC_SWMk18_FqcotVMy3ljucuTJHuBHsh4WC5syNKBOcRGGBHIwOOEmXVbaKi6bVlCLQhDCU1VzsOWRjqlybQNpKd2t7XMcj6lTJVjcwuqiHMRkcKdX4QREpmYiN00WSWEgEfrwlmATTAYv50R0QDnnczRyudfgxwih0xFgr-bDIzZetfchPJ4xWJuEXMOXEQb7ITD7iYhXGSjvn8DVvwTmkI8qHZYcUbdM3hloyqkd1yaNy83vUcjE3iONRFloUNjgRlEdfq1nCEZ_ErtfQ7Tx1XFhnx6ueDM8K7brL5A2lo12UAVN9HSX_z5V_bXM2oU9j-oK5ESX-3fa8dO-jq6CqK95t05Jq5K8BpQR8a-V2q0N4tCWwjMhh9jJPhSlaAhfJJoxD6PTQQTtAhOJqeOAkwSBLDa5Uu8XyfgJpy8

### Tool call: read_file
{"file_path": "workspace/README.md", "offset": 0, "limit": 300}

### Tool call: read_file
{"file_path": "workspace/CHANGELOG.md", "offset": 0, "limit": 200}

### Tool call: execute
{"command": "python -m pytest -q workspace", "timeout": 120}

### Tool result
@@ lines 1-5 of 5 @@
# inventory

Small package. Run the tests from the `workspace/` folder:

    cd workspace && python -m pytest tests -q

### Tool result
@@ lines 1-6 of 6 @@
# Changelog

## Unreleased

## 0.3.1
- initial import

### Tool result
[stderr] Traceback (most recent call last):
[stderr]   File "D:\ViAI\Track3\K4-DAY20-MULTIAGENTS-DinhDucLong-2A202602633\.venv\Lib\site-packages\anyio\_core\_eventloop.py", line 204, in get_async_backend
[stderr]     return loaded_backends[asynclib_name]
[stderr]            ~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^
[stderr] KeyError: 'asyncio'
[stderr] 
[stderr] During handling of the above exception, another exception occurred:
[stderr] 
[stderr] Traceback (most recent call last):
[stderr]   File "<frozen runpy>", line 198, in _run_module_as_main
[stderr]   File "<frozen runpy>", line 88, in _run_code
[stderr]   File "D:\ViAI\Track3\K4-DAY20-MULTIAGENTS-DinhDucLong-2A202602633\.venv\Lib\site-packages\pytest\__main__.py", line 9, in <module>
[stderr]     raise SystemExit(_console_main())
[stderr]                      ^^^^^^^^^^^^^^^
[stderr]   File "D:\ViAI\Track3\K4-DAY20-MULTIAGENTS-DinhDucLong-2A202602633\.venv\Lib\site-packages\_pytest\config\__init__.py", line 253, in _console_main
[stderr]     code = _main(prog=_get_prog_name(sys.argv))
[stderr]            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
[stderr]   File "D:\ViAI\Track3\K4-DAY20-MULTIAGENTS-DinhDucLong-2A202602633\.venv\Lib\site-packages\_pytest\config\__init__.py", line 223, in _main
[stderr]     config = _prepareconfig(new_args, plugins, prog=prog)
[stderr]              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
[stderr]   File "D:\ViAI\Track3\K4-DAY20-MULTIAGENTS-DinhDucLong-2A202602633\.venv\Lib\site-packages\_pytest\con

### Assistant
[{'id': 'rs_0946b25d25cb72ba006ac48d4a8adc87d0903bafa8a31e29a3', 'summary': [], 'type': 'reasoning', 'content': [], 'encrypted_content': 'gAAAAABqxI1S6STNk4lH6zo1y6lTM2c4bOy7_3bJnf6iiF_cULxMnoOlMQA96nWxsa7MUnh2dTeNqcYF9FQUtuYr-CrfxghmF6gofXbLbH4UkW2qwmkiKsFtBfSH53VA4MAKrhSi37E2ADZ4KlYvWWRXfcEMJq6dco99OO0ATyBH1sEAsLNM6Jt3iAl4myO2GdzVBSY82RI48v6WSE1u_bEVge5c6Xq-rJSB1EQY1d0AHhu05s9vEroNhMfIo6qsPuh_qokTl7iYn3gu-VXCFfC-gHWWTEkZxNJD4FkLOSuS83f1GFY7evTV_SubXC9WiwEeW7gmgwMacHhn3BnNZATneYXT2qypHhX_1yeb-t8rMgNZe4ZRfEV4mwDTnmDrbaJPVhvGyskwcVy_pr4D7vJp52liPqk06jRJo3YXt_TXLhuc8T9pG5BvdhFQXjRBfTG04JCBUaqrg469lCCpJU_5eg39JCYPwpzD0ak4YOvb5J4WNpBjqr69eLOeYaKsEEcvv-NmS-PeT3-qYCZ-sJsJhZtO2-bXrO9fjEUu9SxeB7fsX5rcsRgyEfgRkGkCEeXvOLero_NbrsJFmBmZSjgHJDW4J6tBscg_IUzHBqZLIBRN4kmyWyLW14_nCb5GUpezMkrhoFRKGh9HHRXxCQcMzjzoIhEN3poWtcmwDh06pJl70GRWtw_zaqlUGaf9YSyN6cQIJiUN1VfLRJMQdUQUhrvOLhdz5CwO95TV3bUsJgWxg5oSqaSZF-9AEHynQWAnmdLANS8kzAXdlV_7wHvuVfj71FOfwvqH3o-zxDUb1y3AOV0rbE6JBhrrn8p5o6T-pfAKe5vZF04ELcqVSyc6GrLspv6PwoU4GsZ7jwc_L1OPPuYYT_AvaCFuX5hGKiFEtYiMDgrwo5lHtM73vWtXIjrHMFzBefVr3CJ2E9WxUr7HHe9c2t6fv5LOjH_id93YYFhPzmLBHgc2YQFjT_Wgfu0-rQB0_xKHjRXAFNusom5giR9sOs8Oqdfj6o4AH4lfQZvwpu2lqwwXoJdIxlYz8fp-9MbTVjFYJOKOc0gKxl419HdagQTnnNBxoyZnDQ6NBEgyTiC8WUFvPItIytrvbv9auxBKawMnRuKGV7g2HveppyK-Uql8csmc6BKfi5X95E3X0bw37CsHbdoSV1M08Yd7w8B3BlkT8d6xoscBItWTHcXdCeYx-lUZPcTOnWyFn9HwsCNnkaSxq9909rLJbZg3vcwqng0A6RXupSd7YQ2HOseb-pDJi3gdJANHiIiyvx1lyHs7pYYoXJrPNK6T_lvOItyleCrmPCNzE4juJowtGNZC_sCRt_5fjUisnzQ1ASIpbs0vmG37zx

### Tool call: edit_file
{"file_path": "workspace/inventory/pricing.py", "old_string": "from decimal import Decimal\n\n\ndef parse_price(text):\n    \"\"\"Convert a price string to a Decimal.\n\n    Supported formats:\n      \"$1,299.50\" -> Decimal(\"1299.50\")\n      \"  12 \"     -> Decimal(\"12\")\n      \"1299.5\"    -> Decimal(\"1299.5\")\n      \"(12.00)\"   -> Decimal(\"-12.00\")   # accounting style: parentheses mean negative\n    \"\"\"\n    cleaned = text.strip().replace(\"$\", \"\")\n    return Decimal(cleaned)\n\n\ndef apply_discount(price, percent):\n    \"\"\"Return `price` after a percentage discount.\n\n    The result is a Decimal rounded to 2 decimal places using the\n    commercial rule \"round half up\" (0.005 -> 0.01).\n    \"\"\"\n    discounted = price * (100 - percent) / 100\n    return discounted.quantize(Decimal(\"0.01\"))\n", "new_string": "from decimal import Decimal, ROUND_HALF_UP\n\n\ndef parse_price(text):\n    \"\"\"Convert a price string to a Decimal.\n\n    Supported formats:\n      \"$1,299.50\" -> Decimal(\"1299.50\")\n      \"  12 \"     -> Decimal(\"12\")\n      \"1299.5\"    -> Decimal(\"1299.5\")\n      \"(12.00)\"   -> Decimal(\"-12.00\")   # accounting style: parentheses mean negative\n    \"\"\"\n    cleaned = text.strip()\n    is_negative = cleaned.startswith(\"(\") and cleaned.endswith(\")\")\n    if is_negative:\n        cleaned = cleaned[1:-1].strip()\n\n    cleaned = cleaned.replace(\"$\", \"\").replace(\",\", \"\")\n    price = Decimal(cleaned)\n    re

### Tool result
Successfully replaced 1 instance(s) of the string in '/workspace/inventory/pricing.py'

### Assistant
[{'id': 'rs_0946b25d25cb72ba006ac48d5399b487d0975599e526e2de4d', 'summary': [], 'type': 'reasoning', 'content': [], 'encrypted_content': 'gAAAAABqxI1UZMJ7EeOsNqpmySVce_u1XFS8wjdH_hs-ww3yupadMdPKxwwJUvQyjvJwK_O9Kgf-4cbPeOogGfwdyRGQxMsWIrGc-VTJWTOX8FE472KT0jAEz7VHyBfQFxyHa6BQKZH-AdaTyOsln42u4wi889piUyEb7zycsm5rCcnMet_2TOWbdRzgVTnzq55NBtpa2ENEe9KHAVr_0t4EvRj6yzu175kiDIzj3hNYkglBe8sl7KRR2e-aPgF2cZdev5azpxIvRsszNvdqWPRjrtKZVGeyokOcFySre_U406GmbyOj5PCkDlu4ZrrqdBN2MCddsWxx_8l-AsrmG_T7-xFDuxyo2af4cXDmYClaZgvuR_YxfS2uooc--3LViCBZ7Zvyx8Jp5E9dEG3Yr6LTuDii2RJAYIFmGoUZGTHtR5xJWY9vIZgJKLwnL846fS_7j-ucF5Bzc85ZQIp6vuLjSsKjjD4kw75mjEQGfmZIr3ztWqDJ7AEKyx1z11QsynzJreqst_jD2mKamm-CYfnOfUyAI8h5l90hXEHai4eg9hCcNSORFjmtiT4YWLbacW-aEOzV8UGBXbQq_vaQQ-mI9uqpmRLcBpsw9H-Kd5bVaEoOH-OEOw65cFO62gTlArn1rhxItq2a2DtZt4jMZs7aw_NohlBOUu4wjOgQkkJB1abSpr_DXBvPXrY8SXFyT8tFqMhKNFOMRN02MMY13t-kqlvtcFeFmv1eE2OEiCnTiWiORK5v-o-Pi2xO-U7eIgQ6dlfx40u--_e7iegGNFv6pZgm0E0SShcv2zdqlxSEK1QbuhITYhXn_i9vFYRyzObxyMuJgNwAd_MjMgL24QWd2IP3nu2BquT0Ad0e09zIZlzvDDKLnjETPgOCS1edAjbGCe8DYgYi6Lr_G4zlNtVATrpfTb2dBwx_3cL5mLqe1KslAN2wmv1cLj0dZiCddxWRJ33kev_nh9mSte7pL0nBT53kb-BOjyaMjO0pT6Wr4GknzjOk5SUpwA2ggrn0OPKkuJMd1kCzfjMl32z-G-hxPafZLuMM2oCIYNhYIx1_qsHhM6KPLxREu1Qmvhloe4jQrdvFXfOi0ExOx4q86MiiK2OEfHcTNnThv9GjSJBFGw5jG6Jm9OEoIdxbBCT2WeJ0yJ6beDR_WhUF69pW-aU7eNwor8z-5WcYMMjZlz4uMXGKFnvkSs10B4uvV6el6nVqgYLOeNow-ZUmsD-9bJRxWGyDI__IMRDvrzI8v_RYI3e8X3Kk6peTx5Z3NvejTDutDK7omL-fWMyDmpA5pqeI9n2cVPjDQeMvYhE86myd7hC7XURM7NVSycurtoSV8qkjHR16_RIG'}, {'

### Tool call: read_file
{"file_path": "workspace/inventory/pricing.py", "offset": 0, "limit": 100}

### Tool result
@@ lines 1-32 of 32 @@
"""Price utilities for the inventory package."""
from decimal import Decimal, ROUND_HALF_UP


def parse_price(text):
    """Convert a price string to a Decimal.

    Supported formats:
      "$1,299.50" -> Decimal("1299.50")
      "  12 "     -> Decimal("12")
      "1299.5"    -> Decimal("1299.5")
      "(12.00)"   -> Decimal("-12.00")   # accounting style: parentheses mean negative
    """
    cleaned = text.strip()
    is_negative = cleaned.startswith("(") and cleaned.endswith(")")
    if is_negative:
        cleaned = cleaned[1:-1].strip()

    cleaned = cleaned.replace("$", "").replace(",", "")
    price = Decimal(cleaned)
    return -price if is_negative else price


def apply_discount(price, percent):
    """Return `price` after a percentage discount.

    The result is a Decimal rounded to 2 decimal places using the
    commercial rule "round half up" (0.005 -> 0.01).
    """
    discounted = price * (100 - percent) / 100
    return discounted.quantize(Decimal("0.01"), rounding=ROUND_HALF_UP)
ك

### Assistant
[{'id': 'rs_0946b25d25cb72ba006ac48d5618d887d0b90cfde9df1b80fa', 'summary': [], 'type': 'reasoning', 'content': [], 'encrypted_content': 'gAAAAABqxI1af3NKmh9g92wL9YU2zWBf8GxuvdJ9lYyiDTtxDcYMYTCuep1oEnb0UdY3VeYENhxE8drmNr4I8ChEOq35Gx958L3HbvLK4hmzIr9BzMHAzd-ZpXx6be0KLr5mPs62Az7FneHMb1jhcrw6bzbia8h1pnuGj9_LgnNlXdCdWm8BdXMKdUwlnwa7Js-1Nv2unGbrnVSQm4rvB2d5y_wtemY0bJVDeKEk3ouxbl53lwWul7KJplVScEjbsWW7US_3tAWYjsBwJw_7Jsn7xNyT3wH72gV5gMzoeULALqXaFm4JeET5HncFhoNC_tXEa9-nPsbK9ziaOGJCAcHYXjOcXE3DV58_tdshbODe5PqGbH5eFCz-UK0hMBzpy9BUTl-iP7029ClE6_9-WiLqQEe2HcUlunlEkbGwJ_E08V6EMMqbYuw7OpaLsL8OMbWqpdRmttwDCrEXbCqe5d7ulDVzO8PfnzTD_Y87RjO3Y0Kr1U_vVk80wYCK4gsqVlFwwc9YLbzRU7KUmxU5Y5NRTG-Z_AvPUGbQqsY3LVSb2ROED936bpiVLRvhC-wOMl0_jTolkQIGvT7qmU9V3PmRkk2z1er3ZoPN1kvnchmdCyHR2GBy2iB8gIurJZs1HDsD70jtW_K4siGsOKMLIv3xY2ToIs7a6ZgcMs2TQ1P2uNNs820QkvvNYv8nWxz5Jqm1Kk5ZvJyHtx4Nd2QYe-hZRi4KyCMiJA9LdxjxOJBYg4BSi55XRSe53vwuNmZQCp5kccyd9zkLhFWnUUcDNCbJfdiqB3xyuxpXFExgXV-6S9OAC_O_EKbKv78lUSg90u_gpg-fW_Cg6JARaAzkVyzPCuaUPhZKIcy1EcmgV18WWE_cMnPIpkYTpJjDSAXg-t_vj4vWz82iaiE4mOMpNWMckVfeMpws0hkTgjOMxuhpLByiZzl2O9RxIzU3JyVT4iv-5qGTeEaGvmhPnx66sCHAL2Vk1tN1_6h6Gi8xxAHvu1QFX59RNBVEgczqp9LV3teU4gKYbiNFf--eUmHhE7R6RvkECaJPA4pLX6KEzdY1YtsBtlGW7Zez3StjhhUTt1ZpkLK5kondNBSwGYrCLQRJnNxsr_YH6LvLuuUV3kSXmmoVMWi_ilnasWtKDSNcknmPurzkxzJ5M4dVBaoFRXDS7RG4KjUKyKHDv4l1Om4ATqUNo02AQd7IsMYWIZNiRVeXLyZvoyqohRnWCFdfzfTPH5NGGoS7LgT8D0dXdB0oKMJFYCgRrh4='}, {'arguments': '{"file_path":"workspace/inventory/pricing.py","old_string":"    return 

### Tool call: edit_file
{"file_path": "workspace/inventory/pricing.py", "old_string": "    return discounted.quantize(Decimal(\"0.01\"), rounding=ROUND_HALF_UP)\nك", "new_string": "    return discounted.quantize(Decimal(\"0.01\"), rounding=ROUND_HALF_UP)", "replace_all": false}

### Tool call: edit_file
{"file_path": "workspace/inventory/export.py", "old_string": "    price = parse_price(item[\"price\"])\n    return f'{item[\"name\"]},{price:.2f},{item[\"qty\"]}'\n", "new_string": "    price = parse_price(item[\"price\"])\n    name = item[\"name\"]\n    if \",\" in name or '\"' in name:\n        name = '\"' + name.replace('\"', '\"\"') + '\"'\n    return f'{name},{price:.2f},{item[\"qty\"]}'\n", "replace_all": false}

### Tool call: edit_file
{"file_path": "workspace/inventory/report.py", "old_string": "    return [item[\"name\"] for item in items if item[\"qty\"] <= threshold]\n", "new_string": "    names = [item[\"name\"] for item in items if item[\"qty\"] < threshold]\n    return sorted(names, key=str.casefold)\n", "replace_all": false}

### Tool result
Successfully replaced 1 instance(s) of the string in '/workspace/inventory/pricing.py'

### Tool result
Successfully replaced 1 instance(s) of the string in '/workspace/inventory/export.py'

### Tool result
Successfully replaced 1 instance(s) of the string in '/workspace/inventory/report.py'

### Assistant
[{'id': 'rs_0946b25d25cb72ba006ac48d5d7a8487d0a744f2d5ee151d47', 'summary': [], 'type': 'reasoning', 'content': [], 'encrypted_content': 'gAAAAABqxI1gMjRUwouws7GFINty6PuA5vcpMnLVBxg7bw7tznsrBl2rnKkid3riZKB7OBjT2xbzAGsLAfRv9qoZX900a0oU7HlxyGZxTsrcmxLP-gdfxGzKmv9QdVzHuLVbDh7aMfDPKmgzcAA0aAzu1kL4nWIPq5JcjORKgAeOSRWJeKWLNlJZaEMXj5sKrWzcSPVMkfguekFeOIM81C6SzKjk5e0-iLxo6_lDNqwtbG0yTQKgxWkzR2vPiXbPruTAHUooVP124aOG8Pbz-w67LcGy1E7QAOyUfOQ1E7ZqhQtMOvQphE_DqQI-jH3efacn3zIBYJi8HGwydMshq1Smqsq8RYIrDAbmGrD4sd_vB2EHx_NhGzXa-22xXW2epRkbOdRWn4wiEzHfhfVFRdwsp-0no_fwR4Bxk7fA4SnfvMR2pQBVIhFpWKXSZ1nIk2biMlHdeopRHHxbBYjsQpu0AMnwIvS-HtTltb51UpnQq8DUXBfnJ-wUQQ1TCFtLyQo6aSjjPBgcU9i9qfWavTsw1-UbR6pMkcdFNmnliVCyUVTU50KyMG_DbbDnfmRA-OEV1bbAHDBSUcPXyXaMfPT-8v_i2EcPefM59D2fs_RS21_Ure40hH87a_DnuVHpznc-ifoQgTgjC-wb0aVjozlra3VlMnYB_B-dImlFzNmu_oRpfqiEJVaOWcLD7WNU5qezvPGjX0MDYcOKxVw59KMtzPLen-fqZmlPSqmwBEykZWEzkdxaVgHztPtjs3Y7jQ2PV3gXjEc3872KEmb32u5LifLU5Yd03yTCn4zK3I0Gii1I3Go3it7Wec1aiHLJoWzFiO-ZYuJ7LafLm_V060V_Dl0z5rdKjRZqxPBduJd8cIz1pe80z29rVDrcZrLk3wogE-3s7YriSQsVLv-8csEVE4fW9oci41-N0nflEF0JbEXkS_DHcRg9NswL_atDCnYZjNrx09vcAyHOifGBOkwfYLmOTue_trdlfn8_fVKESZrAaBzly7HYVRQUR1p02_91svlSpkJYAOaqtPxFuJEc6tN6wt_kHvxe9L7iaiNeGZvXpZAf7330jMBXkEoEbQUGP9751yqz_ki9a9t6g6buSn0DX_gRILiD1Aq0IL81tpp4JBWD242vpFuf-Afi1WrNC4OHb3T65YWlq-jmfJYzDowEDJPc4_aRuSkqmBrW4-K7ldBdpQaUbKO0R3XhgwGHmgzqc1THvxVerk64Y6ECSdxqGJ7JiQZ9mzYXkvWGl4mmUWgP3wkYX4LyTOfIFKuFpRpZKkjB_tK4vZW0Fa-lT7JlPkD8aMhZenPoeua4592bROC-MEl5OgivDG9JXXOY78K6Pix10fgGju

### Tool call: execute
{"command": "PYTEST_DISABLE_PLUGIN_AUTOLOAD=1 python -m pytest workspace/tests -q", "timeout": 120}

### Tool result
[stderr] 'PYTEST_DISABLE_PLUGIN_AUTOLOAD' is not recognized as an internal or external command,
[stderr] operable program or batch file.

Exit code: 1
[Command failed with exit code 1]

### Assistant
[{'id': 'rs_0946b25d25cb72ba006ac48d62622087d08b4dcb962086840b', 'summary': [], 'type': 'reasoning', 'content': [], 'encrypted_content': 'gAAAAABqxI1jY2-KsIX3te9Y0Q4wauVRzSkqZcn4aARDQBREdYNtX5M_2BCw56cUD2TH4Fj9QPh60UskEtTZ1puw6e5f9YBgWULYSFoYM1xvFZCrTqooWiEQPvDPkwfG3WIIVY52oZLvXoSMarfsATzvQi2Kk89Qy9mlJ8KED9AEcoLdFCSJCB0rW9iQe2I8adWbkuLSVUKUYUE-LascsDUfgOoKy7LqfvMS03heut96eHNgrctN7_8ikjYGCG0te_RyHzt4Gup9BQqr5US6k2sl-9M7-gM5_m0jP-GX3n8E7G04ikkBPVM9dMZyz0w2fs5uDkP6VQrl3KOVIRp8qiWeIs54EAME1rlIGaF-o80KFwfHFiBMOW65Ax_OLWzMHNsaHRE-Uqj2cpLDt4GKZ82EmUw8a7yGqv-gGADts6_yBx2LIhXRO4dE7LB3LftgdCqxaq8cqV9rK_G-w3dMa2kSIZ-eKc9Umoueh0QN1U6GjQUCc2coZWa3eHdRGJBe8GR44iphaywYXsrwk56Vl9MVtdE7F0rqUU_FhaoxohDB4DE90GyJ2zWxXpSgAN369x-u0OISuGZU9sRC1ZjGMGFz1i_iADZNRA5dpNqtxXK3uGfZ1rsNEc9iDWyzGX3LHli91wDOkYh8F2SMOA23jGdLaVIQCrzCdki8EMXyEH0DTgyU5rB3mDmBexhlyAc3kwmiIlWEcFN0bdW4esE5pYbGJMMBO2lVdmA1oe81ZrXxdaIqN18OakGN8uu0kmKA9gvhet-z4kUoJxESf87j9saNzKYoOrqjjyiUBZazSg5u6n4Jzu7AvZyGip9JQMqnL9bdF3_Bto5_L58oDqCkDKRWXfTKUVcZYs_9L7fXXfOxDMc6Gj-SjQ4WiIPTEJrFO8lydWfVXG0O20zncKBQlbd3jvb6jq4mNJRSNP4VGlZzhwLvFnRQnXAOD_bw8WFfc6As3Tyi7osy4tsjJlRn1WTT0rwCUhmv1wvGVbeS8z0__GbY8fFVTA66kYT-JzMmye50XdLPJbxpggJU1jhwabdguu3GSER3LszBU_gH7MOKa6OAFRL8BtYLuJ1mZxRmpiJkfUI88p2QYMqlVnVzQppXtH2kLHvHUeUfiFRdL6gthNotP8YF8OidxyemaJTHGPPioqviS26Vb4kvIIqvy80OMHfyw3wTIilT51jRz3E4p02KSZ5_452sm9axg8a9a90Nau-r8d3tGDW7L9WyGUoU3o9jnRO98nYOJkDNnjP0NY_JmvGQUE0iaHWAtSIcu4XrfWF8py6f2RF5TY2VvI_P5pucJpprWQ=='}, {'arguments': '{"command":"set PYTEST_DISA

### Tool call: execute
{"command": "set PYTEST_DISABLE_PLUGIN_AUTOLOAD=1 && python -m pytest workspace/tests -q", "timeout": 120}

### Tool result

=================================== ERRORS ====================================
_______________ ERROR collecting workspace/tests/test_report.py _______________
ImportError while importing test module '~\AppData\Local\Temp\tmpq56ja5go\workspace\tests\test_report.py'.
Hint: make sure your test modules/packages have valid Python names.
Traceback:
..\..\Programs\Python\Python312\Lib\importlib\__init__.py:90: in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
D:\ViAI\Track3\K4-DAY20-MULTIAGENTS-DinhDucLong-2A202602633\tasks\code-learn\workspace\tests\test_report.py:3: in <module>
    from inventory.export import to_csv_row
E   ModuleNotFoundError: No module named 'inventory'
=========================== short test summary info ===========================
ERROR workspace/tests/test_report.py
!!!!!!!!!!!!!!!!!!! Interrupted: 1 error during collection !!!!!!!!!!!!!!!!!!!!
1 error in 0.25s

Exit code: 2
[Command failed with exit code 2]

### Assistant
[{'id': 'rs_0946b25d25cb72ba006ac48d66692087d0b563ce4fc368b6ab', 'summary': [], 'type': 'reasoning', 'content': [], 'encrypted_content': 'gAAAAABqxI1ncixLqxX9ganvn57JTc9BiYx5xbTH9uK9xZkJ0I1BnqnTib5VwGfEw4gYHMwO1bObldNO-LLyx07B0dorxCwFVMOX4CXn3CpSutwzDoSNHrMbpgihuVc-SdNN1dL8w3Dfdhk3JWPjfDFKVX9oDAiJM2zLBXtQGMKnWzj7dhMyYxM0efZc8BsOa3wX5OU0JALweyalKwnwHiQyANSaJAYKswD1_ljXhHoRdnFknoU84GIhKVHv_NIxOdgngz7jK6pRGV9j8ef1nST2ByAqMDUGrE1i_MwHo-6iSCOrA3w3RAPbSLXBmI9QpFAlcHkFMKQIywglZvxixfvRrpPUct0w8DtZsRA0zMjMatvCUBaQLMoXAPa17iL7Uz7vxmNk1TJ1rB1zlCcFvPfQxx3fInXxf4jFKruDyiwp0Et5TAYSshmOpz0ikutVBkOKTsVXHBbnrW1rziZZxqo9EYd7A_21oeWDJUtSuSsE0HSPmyNyZS8_XfuK_WDlV3HU836Djsfh5P6eWgUkGXV97qaQhozbEFSepE8pgAOghs61BGnZ7mfyM3GPML0iY67gbDvAVJ0PykyPyNN9a6jRisLtdRiIlKCu3XPUIVqT_M5XOwmkJHDPQswnsJqLxQXyDE51GNFUQuztCzz8X291jRMAGiJNNdIuXKiH0c-8LOf5vlYVqMrL2fIzyikKsD4pje4Ll6xZSjO-lP5sjjjXW7X3ho3kgUU0JpvKJOhEp6Sq2MRnVduw3Y37Sg6ek6vw9OG1_VL7sgfG3308yFOq7TLG1a5bZnKKeBRG1wcBVttoh4iVJHSGalnLuDH3eIeVnY5BVHyB0jMHhWSGaFKTwVIhXQ8eJuztnEMtOianKzp3jFDJPCkT9cDsBu-Nt-Yiq-32IgcAa3SR5Fh6-9ryh6U1LUuY0scPqfjapivXHLS7z575N8CcAYG2gBLcux8Ksc6sDzycxA0CXQJJSlwj3dMfATXhHOFFvuSAIFtBMlwqS48yn9SfdE-nnAxN4d0yr5qChdnFBqVEfHTalIQX4-H-V0qheOvOb_FMQBCHStV4EvjBQ6USGmZ6PomYXHA31xh45ZPk4jdTuXrKO2OM-X_JumcNRtXFjF8DYDQ_FAf0KxwU8FoVvew2sb_YLIO_dOECl9EMHS5mn9vx76YGsKZr9ISKfQugw7w-_LV2JJPfnknqwRSrAA5sFQZ831g-p-LIa7hXVjANgB9GLnuCQpQt4dF2pbq8gHP_oKfA9dzJOv--vDMQ1MHv2jOh4DOdS2z3c2cB5y0czN9EEPmPlcXckgIeptYuYzzA2geWqdyYQNZamWidnsEL_ZkWK3WiBVsukOLLmcSWaE

### Tool call: execute
{"command": "cd workspace && set PYTEST_DISABLE_PLUGIN_AUTOLOAD=1 && python -m pytest tests -q", "timeout": 120}

### Tool result
......                                                                   [100%]
6 passed in 0.09s

[Command succeeded with exit code 0]

### Assistant
[{'id': 'rs_0946b25d25cb72ba006ac48d6a171887d097a930f391cca974', 'summary': [], 'type': 'reasoning', 'content': [], 'encrypted_content': 'gAAAAABqxI1v_eF54-CYHvSp53apeaa6-Q4JZ6F-RGM79gWV6smAZ5HfBzfeDrPEcktFWdsCs3TOwK1g9o4qlkUS7h4E_g6CSw3rdjKQJAPlRdFbrkvVl5wvNiv5Dv9DIS9EG9t294cA3_7_vcjS2mmMw74oz81lIjc0UCTFbABk_vvWS1If27Ih1ocqatWENfV0gDOL78lKwPHyvvdtNeWLKaAB9ZPLH5p85IsSaUyuEItvd-RFwsCCa4pGXd5CYMMTnKfBg1S4NnMgQkoxDjVqCZkX9y6Ydkqkm-evy2W-rJg49H1acLzDV8X7bnXYXCilfHns3kry4tkML0cCytIAv0pXcRz26AEZd5mXkkTNmJKKjMQwAjXcZvjif0Rdl8_0d7nnXtXm_U7CHOOcIEGHTPqq41CHCy_W04DcYcvKdgjq8M-WagZezNeIUymFY8m5QmFd7j7zQqmJrnrnFw4vMJ70JY-yt0L_ySpGY9Hw2hXSk30rqi9nE-jmCXvMo_u_9cBgi9VfmPR4rwVAYuf2hAHC9u0ndSkhiApL99kwE4dOMl3MFU3UrnXPWIaETtK1gykAVXrQvK-IlAgWs6Wn3pvLVC6ndYzaFm77I2auCAROpaY3k5R76kZc3jOAQZ0_KRX1eBNEYEtR0QeKYM1mgkweGtibezDowBSJQ3KQ2dSL3CPT0k5k2xVLprkIZugtFf15tBY2hqbb2wjXWT8ntpdtTcLxqt5ubT4qEo5-xtMNjkH4skjK3yhN-gS6PrSQxnVtpTnecBEBfXdP18B7CMMq9K01Bf12LUPGbs8Dzz1w2uYoYXyou4W1s6DxXMToyduLuedzCStI8q7xqLBBZyFVb-_VAzJqsC2eBR0H1fUuWYXq5lb0Z8EWS5nRTUAa-lHQCCifBD9df5YRcSPY4tJ_GvWr9LMODzf3sX0gTDGPu-XT2DSKkgg32qQQuswgS5U7XpJm9a8vg_iiBnQvFwUKkw4doZbJR0uhXprfm3uk9RQ7TYaJBAL_8MnmI_y7gI7qB0sBG4ElyYskyprfJZzySVyNASHEUrV0WttgNQRU73A1Wa4egGnnU43yKVwP9XsbH77o0mVHyhztedZgmjtjyw0h1lRi2d66VJVyqFgoclXY4ZDQDIw5Sdwft23BdNuK4fTch0qT5fImWl6RWPBIqQbd59fwOYPZGq0E3h_DwQZRz9TDkCOuDhRg4lqQ2Epwl88Hg66gfKwYY4XFLv1nf6Y2mDcJiUKba8bfJ-Cjev3zRJgCBDA9vtQrUgJo2YIRpmGmNIHdxS1A92b9thf29X_97VS0It7rj6Zfi819oXnZYzRmuDpA7u1UupLUi291u28kwi9xwU

### Tool call: read_file
{"file_path": "workspace/inventory/pricing.py", "offset": 0, "limit": 100}

### Tool call: read_file
{"file_path": "workspace/inventory/export.py", "offset": 0, "limit": 100}

### Tool call: read_file
{"file_path": "workspace/inventory/report.py", "offset": 0, "limit": 100}

### Tool result
@@ lines 1-31 of 31 @@
"""Price utilities for the inventory package."""
from decimal import Decimal, ROUND_HALF_UP


def parse_price(text):
    """Convert a price string to a Decimal.

    Supported formats:
      "$1,299.50" -> Decimal("1299.50")
      "  12 "     -> Decimal("12")
      "1299.5"    -> Decimal("1299.5")
      "(12.00)"   -> Decimal("-12.00")   # accounting style: parentheses mean negative
    """
    cleaned = text.strip()
    is_negative = cleaned.startswith("(") and cleaned.endswith(")")
    if is_negative:
        cleaned = cleaned[1:-1].strip()

    cleaned = cleaned.replace("$", "").replace(",", "")
    price = Decimal(cleaned)
    return -price if is_negative else price


def apply_discount(price, percent):
    """Return `price` after a percentage discount.

    The result is a Decimal rounded to 2 decimal places using the
    commercial rule "round half up" (0.005 -> 0.01).
    """
    discounted = price * (100 - percent) / 100
    return discounted.quantize(Decimal("0.01"), rounding=ROUND_HALF_UP)

### Tool result
@@ lines 1-15 of 15 @@
"""Export helpers."""
from .pricing import parse_price


def to_csv_row(item):
    """Format one item as a CSV row: name,price,qty (price with 2 decimals).

    A name that contains a comma or a double quote is wrapped in double quotes,
    and every double quote inside it is doubled (RFC 4180).
    """
    price = parse_price(item["price"])
    name = item["name"]
    if "," in name or '"' in name:
        name = '"' + name.replace('"', '""') + '"'
    return f'{name},{price:.2f},{item["qty"]}'

### Tool result
@@ lines 1-26 of 26 @@
"""Inventory reports."""
from decimal import Decimal

from .pricing import apply_discount, parse_price


def total_value(items):
    """Total value of `items` after per-item discounts.

    Each item is a dict: {"name": str, "price": str, "qty": int, "discount": percent (optional)}.
    """
    total = Decimal("0")
    for item in items:
        price = parse_price(item["price"])
        price = apply_discount(price, item.get("discount", 0))
        total += price * item["qty"]
    return total


def low_stock(items, threshold=5):
    """Names of the items whose quantity is strictly below `threshold`.

    The names are sorted alphabetically, ignoring case.
    """
    names = [item["name"] for item in items if item["qty"] < threshold]
    return sorted(names, key=str.casefold)

### Assistant
[{'id': 'rs_0946b25d25cb72ba006ac48d71235c87d0b8c248f2385d4585', 'summary': [], 'type': 'reasoning', 'content': [], 'encrypted_content': 'gAAAAABqxI10ZT_63pjTyvx4F4n7kzW3WYa0w5aWDCfYLPGIWQ96wb9Q6YhVEoIP8JbPSa8emTkpkF7c77j-2xakVMnH8D3aE1TsZVqnHv7BmHVLajv4eDtTEYTsrGfsr73i1VuP8YLq2j1Nmt5hdOGRsW49SFwYRsrW1gU1E3RlcOOVColsOpBqOVV0gx_QvkD-Yg60eRMEFuR70xYp8AhaYpma1yc-77TP6DyaLt6S_OHJZY64FGqK0A09XEExMKj3zTS9ZVcZBUUuYjpd45nelomX_X_8mInTPIJWVPb7nlKOvL1enYqu4zJeee3K-UHOjx2MFNT6w-lIhBw8yx0XUs62dEf6CWO9mShp9LsG5ChNKBwcduROkAAu7z71YIqudRU_LT0CxpCVpZAt_cF8cDMq08exS4oEIKFB8MFgUANC2ieEjHJqlaRz1f5ZM9OAYqu0J22N2davOr9BmGS0WgIgfW-_cFW2if2k9Trn01MkN52qbVEg4d-ALZ4rTMbCy0pa-TqznIt9bCQmn8u0082alckQ_VBWew8Bd3TIPPE7BTB7VLO7Lu5Lu9ec5HtWZMR6UhvbRFCsfYlDVbjMYbuW8PeYR7ogxJzWV4C4mauFakF-OFv7lUeppS7ztTt2TwZzUOFyDkqpE6di66UzuBb4WKDf6UoHdoOm4ygSLaRe3fRIMZ5shJcFsHnrdlalbgHtfAyyWv8QMgClNS8njWj3ThSXuI6lsguu9QVtC8kHMVLdUK7MwwJ-eB8kP7ZyJWq4RTDGM55cmw6eYm-KEwblu1HKesAYwE300U0w4YDkWvBtvjDCSWmjci27t8PmD61u1min4eA817OTabicp-WLjcYjchkxRqIqgCogRxJL7JxB3H0XDgILaSgcPOv8DrjtggW8RH3J2Il3LtUdDY-KZdl8LrZIrGx89GexfRBF87ES4nd9A46M5MgjacvUDOhUYRemZeo_y3N-4uJMzzetvtxJzpmRRC-YcgMNTWVPB8nXTkzZn79aQhkTalOODFuoa60t2l0hbYziaGK28CIXhJAfxNgBzb6UyZUoFotQ9p2Gwn8jmOg2mM-261Jrcby1UhQqoXB3Ck3XMzG9b_6GGdlT6WpcXos7JZMmO4Oqr9BB6DqCph1UBuh86MYfI5uuZxStaNtyMGJVNIde2-9t3R7XQijFSiJ2Vz8HQNJPPrrqynhZYjIdlBY96WbGP7nF68EgDdgfA71MAhwR5qhD2A8GENEyb6O5-J0DgY8-msxNMlMVg8aolpEYJ2FwDCP-FZBvmSidwOFpshWyi2mDvuLTGQj8UbXsI2IPCLZjreuCoqg5q9GOseo2LddgZEtPSR7ynTxBtz