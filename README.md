Task-01: Secure User Authentication into simple steps that you can understand and implement easily, even if you're a beginner.

🎯 Objective of the Task
You need to build a web-based user authentication system with the following:

✅ A user should be able to:

Sign up (Register)

Log in securely

Access protected pages (like dashboard) only after login

(Optional) Add features like hashed passwords, session management, and user roles (admin/user)

🧰 Tools You’ll Use (Recommended)
We'll use:

Python with Flask (a mini web framework)

SQLite (for database)

Flask-Login (for login session management)

Flask-Bcrypt (to hash passwords securely)

✅ Step-by-Step Guide
🔧 Step 1: Setup the Project
Create a folder:
Example: foldername

Install Required Packages (in terminal):
pip install flask flask_sqlalchemy flask_bcrypt flask_login

Create files inside the folder:
/user-auth-app/
│
├── app.py          → Main code file
├── models.py       → Defines the user model
├── templates/      → HTML files go here
│   ├── login.html
│   ├── register.html
│   └── dashboard.html

💾 Step 2: Create the User Model (models.py)
This defines what a "user" is in your database.

python
Copy
Edit
from flask_sqlalchemy import SQLAlchemy
from flask_login import UserMixin

db = SQLAlchemy()

class User(db.Model, UserMixin):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(150), unique=True, nullable=False)
    password = db.Column(db.String(150), nullable=False)
🧠 Step 3: Create the App Logic (app.py)
This is where all the login/register logic happens.

python
Copy
Edit
from flask import Flask, render_template, request, redirect, url_for, flash
from models import db, User
from flask_bcrypt import Bcrypt
from flask_login import LoginManager, login_user, logout_user, login_required, current_user

app = Flask(__name__)
app.config['SECRET_KEY'] = 'your-secret-key'
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///users.db'

db.init_app(app)
bcrypt = Bcrypt(app)
login_manager = LoginManager(app)
login_manager.login_view = 'login'

@login_manager.user_loader
def load_user(user_id):
    return User.query.get(int(user_id))

with app.app_context():
    db.create_all()
📝 Step 4: Registration Code (/register route)
python
Copy
Edit
@app.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'POST':
        username = request.form['username']
        password = bcrypt.generate_password_hash(request.form['password']).decode('utf-8')
        new_user = User(username=username, password=password)
        db.session.add(new_user)
        db.session.commit()
        flash("Registered successfully! Please log in.")
        return redirect(url_for('login'))
    return render_template('register.html')
🔐 Step 5: Login Code (/login route)
python
Copy
Edit
@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        user = User.query.filter_by(username=request.form['username']).first()
        if user and bcrypt.check_password_hash(user.password, request.form['password']):
            login_user(user)
            return redirect(url_for('dashboard'))
        flash("Invalid credentials")
    return render_template('login.html')
🔒 Step 6: Protected Page (Dashboard)
python
Copy
Edit
@app.route('/dashboard')
@login_required
def dashboard():
    return f"Welcome, {current_user.username}!"
🚪 Step 7: Logout Route
python
Copy
Edit
@app.route('/logout')
@login_required
def logout():
    logout_user()
    return redirect(url_for('login'))
🧑‍🎨 Step 8: HTML Files in templates/
register.html

html
Copy
Edit
<h2>Register</h2>
<form method="POST">
    <input name="username" placeholder="Username" required>
    <input name="password" type="password" placeholder="Password" required>
    <button type="submit">Register</button>
</form>
login.html

html
Copy
Edit
<h2>Login</h2>
<form method="POST">
    <input name="username" placeholder="Username" required>
    <input name="password" type="password" placeholder="Password" required>
    <button type="submit">Login</button>
</form>
dashboard.html

html
Copy
Edit
<h2>Welcome to Dashboard</h2>
<p>You are logged in!</p>
<a href="/logout">Logout</a>
▶️ Step 9: Run the App
In terminal, run:

bash
Copy
Edit
python app.py
Then go to http://localhost:5000/register in your browser.

🏁 Final Result
You will have:

✅ Register page

✅ Login page

✅ Protected dashboard page

✅ Secure password storage

✅ Session management
