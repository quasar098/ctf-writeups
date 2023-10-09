# the customer is always right

## problem

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/8604c846-2434-41a3-8dd7-4e76d7fc020b)

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/1f6ab73f-6502-46ab-bd39-a0fe0cade205)

source.py
```python
#!/usr/bin/env python3

from uuid import uuid4
from os import urandom
from hashlib import sha512

from flask import Flask, request, Response, redirect
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address
app = Flask(__name__)
limiter = Limiter(
    get_remote_address,
    app=app,
    default_limits=["50000 per hour"],
    storage_uri="memory://",
)

class Item:
    def __init__(self, name, data, cost):
        self.name = name
        self.data = data
        self.cost = cost

class Transaction:
    def __init__(self, item):
        self.item = item
        self.returned = False
        self.id = str(uuid4())
        txs_dict[self.id] = self

SECRET = urandom(32)

txs_dict = {}
user_txs = {}
user_balances = {}
used_signatures = set()
items = {
    "fakeflag": Item("Fake Flag", "jctf{red_flags_and_fake_flags_form_an_equivalence_class}", 10),
    "realflag": Item("Real Flag", open("flag.txt").read(), 100),
}

@app.route('/', methods=["GET"])
@limiter.limit("5/second")
def index():
    set_cookie = False
    uid = request.cookies.get("user_id")
    if uid not in user_txs or uid not in user_balances:
        uid = str(uuid4())
        user_balances[uid] = 10
        user_txs[uid] = []
        set_cookie = True
    if "source" in request.args:
        resp = Response(open(__file__).read(), mimetype='text/plain')
    else:
        resp = Response(f'''
            <h1>Flag Market!</h1>
            For all your flag needs, both fake and real.<br>
            User balance: ${user_balances[uid]}<br>
            <a href='/transactions'>Transactions</a><br><br>
            <table>
            <tr>
                <td>Fake Flag</td>
                <td><a href="/buy?item=fakeflag">Buy for $10</a></td>
            </tr>
            <tr>
                <td>Real Flag</td>
                <td><a href="/buy?item=realflag">Buy for $100</a></td>
            </tr>
            </table><br>
            <a href='/?source'>Source</a>
        ''')
    if set_cookie:
        resp.set_cookie("user_id", uid)
    return resp

@app.route('/buy', methods=["GET"])
@limiter.limit("5/second")
def buy():
    item = request.args.get("item", None)
    if item not in items:
        return "You can't buy that here."
    item = items[item]
    uid = request.cookies.get("user_id", None)
    if uid not in user_txs or uid not in user_balances:
        return "Invalid user."
    if item.cost > user_balances[uid]:
        return "You don't have enough money!"
    user_balances[uid] -= item.cost
    tx = Transaction(item)
    user_txs[uid].append(tx)
    return redirect("/transactions")

@app.route('/transactions', methods=["GET"])
@limiter.limit("5/second")
def transactions():
    uid = request.cookies.get("user_id", None)
    if uid not in user_txs or uid not in user_txs:
        return "Not logged in! Please go to the <a href='/'>home page</a> to have an account automatically created."
    ret = '<h1>Transactions</h1><br>'

    ret += "<a href='/'>Home</a><br>"
    ret += f"User balance: {user_balances[uid]}<br>"
    ret += "<ul>"
    for tx in user_txs[uid]:
        if not tx.returned:
            ret += f"<li>{tx.item.name}: {tx.item.data} (<a href='/return?id={tx.id}'>Not satisfied?</a>)</li>"
    ret += "</ul>"
    return ret

@app.route('/return', methods=["GET"])
@limiter.limit("5/second")
def return_handler():
    tx_id = request.args.get("id", None)
    if tx_id not in txs_dict:
        return "That transaction doesn't exist!"
    tx = txs_dict[tx_id]
    if tx.returned:
        return "That item has already been returned!"
    salt = urandom(4).hex()
    sign = sha512(SECRET+salt.encode()+tx.id.encode()).hexdigest()
    return f'''
    <h1>Return this item?</h1><br>
    Are you sure you'd like to refund your {tx.item.name}?<br>
    <button onclick='location.href="/refund?signature={sign}&salt={salt}&tx_id={tx.id}"' type="button">
    Yes, please refund me ${tx.item.cost}
    </button>
    <button onclick='location.href="/transactions"' type="button">
    No, go back to transactions
    </button>
    '''

@app.route('/refund', methods=["GET"])
@limiter.limit("5/second")
def refund():
    sign = request.args.get("signature", None)
    salt = request.args.get("salt", None)
    tx_id = request.args.get("tx_id", None)
    if sign in used_signatures:
        return "Invalid refund!"
    if sign is None or salt is None or tx_id is None:
        return "Invalid refund!"
    if tx_id not in txs_dict:
        return "Invalid refund!"
    tx = txs_dict[tx_id]
    user_id = request.cookies.get("user_id", None)
    if user_id is None or user_id not in user_balances or user_id not in user_txs:
        return "Invalid refund!"
    if tx not in user_txs[user_id]:
        return "Invalid refund!"
    if sha512(SECRET+salt.encode()+tx_id.encode()).hexdigest() != sign:
        return "Invalid refund!"
    used_signatures.add(sign)
    tx.returned = True
    user_balances[user_id] += tx.item.cost
    return redirect("/transactions")


if __name__ == "__main__":
    app.run('0.0.0.0', 15000)
```

## solution

the vulnerability here is that the refunds are each unique so on this page:

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/0a226ba2-dd00-444e-b339-0362a3e4cc77)

opening up this page multiple times on different tabs will each have different signatures but the item will be refunded multiple times, allowing access to infinite money

buying the real flag, it is

`ictf{yknow_I_really_should_have_done_this_in_a_sql_table_rather_than_memory_but_I_cbb}` i am not sure what cbb means but it means something probably

![image](https://github.com/quasar098/ctf-writeups/assets/70716985/b16f7aa4-2e1a-4a26-a159-854490ea5f14)
