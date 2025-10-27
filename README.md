# Recipe-book-app
from flask import Flask, render_template, request, redirect, url_for
import sqlite3

app = Flask(__name__)

# Initialize the database
def init_db():
    conn = sqlite3.connect('recipes.db')
    c = conn.cursor()
    c.execute('''CREATE TABLE IF NOT EXISTS recipes (
                    id INTEGER PRIMARY KEY AUTOINCREMENT,
                    title TEXT NOT NULL,
                    ingredients TEXT NOT NULL,
                    instructions TEXT NOT NULL
                )''')
    conn.commit()
    conn.close()

@app.route('/')
def index():
    conn = sqlite3.connect('recipes.db')
    c = conn.cursor()
    c.execute("SELECT * FROM recipes")
    recipes = c.fetchall()
    conn.close()
    return render_template('index.html', recipes=recipes)

@app.route('/add', methods=['GET', 'POST'])
def add_recipe():
    if request.method == 'POST':
        title = request.form['title']
        ingredients = request.form['ingredients']
        instructions = request.form['instructions']
        conn = sqlite3.connect('recipes.db')
        c = conn.cursor()
        c.execute("INSERT INTO recipes (title, ingredients, instructions) VALUES (?, ?, ?)",
                  (title, ingredients, instructions))
        conn.commit()
        conn.close()
        return redirect(url_for('index'))
    return render_template('add.html')

@app.route('/delete/<int:id>')
def delete_recipe(id):
    conn = sqlite3.connect('recipes.db')
    c = conn.cursor()
    c.execute("DELETE FROM recipes WHERE id=?", (id,))
    conn.commit()
    conn.close()
    return redirect(url_for('index'))

if __name__ == '__main__':
    init_db()
    app.run(debug=True)
