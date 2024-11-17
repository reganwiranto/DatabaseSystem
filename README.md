# 113-1 Database System :
Lecture： 蔡芸琤老師  
Name ： 黃新瑞
Department : 115科技
## Class note
## Homework area
[HW2 ER Diagram](erd.jpg)
[HW2 Video](https://youtu.be/P1NcZ1EhFSo)

[HW3 Code](from flask import Flask, render_template, request, jsonify, redirect, url_for, session
from pymongo import MongoClient
import config

app = Flask(__name__)
app.secret_key = 'flask'

client = MongoClient(config.MONGO_URI)
db = client['entry_mongodb']
db.users.insert_one({
    "username": "admin_entry",
    "password": "admin_entry"
})

@app.route('/')
def login_page():
    return render_template('login.html')

@app.route('/login', methods=['POST'])
def login():
    username = request.form['username']
    password = request.form['password']
    user = db.users.find_one({"username": username, "password": password})

    if user:
        session['user'] = username
        return jsonify(success=True)
    return jsonify(success=False, message="Invalid credentials")

@app.route('/homepage')
def homepage():
    if 'user' not in session:
        return redirect(url_for('login_page'))
    
    # Fetch entries from the database
    entries = db.entries.find()
    return render_template('homepage.html', entries=entries)

@app.route('/create')
def create_entry_page():
    return render_template('create_entry.html')

@app.route('/entries', methods=['GET'])
def get_entries():
    if 'user' not in session:
        return redirect(url_for('login_page'))
    
    entries = db.entries.find()
    entry_list = [{"title": entry['title'], "content": entry['content'], "create_by": entry['create_by']} for entry in entries]
    return jsonify(entries=entry_list)


@app.route('/create', methods=['POST'])
def create_entry():
    title = request.form['title']
    create_by = request.form['create_by']
    content = request.form['content']

    db.entries.insert_one({"title": title, "content": content, "create_by": create_by})
    return jsonify(success=True)

@app.route('/logout')
def logout():
    session.pop('user', None)  # Remove user from session
    return redirect(url_for('login_page'))

if __name__ == '__main__':
    app.run(debug=True))
[HW3 Video](https://youtu.be/9gnmmt3jD-0)

## Final
