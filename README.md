# premraj
 https://premraj17147.github.io/premraj/

from flask import Flask, render_template, request
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///inventory.db'
db = SQLAlchemy(app)

class Product(db.Model):
    product_id = db.Column(db.String(100), primary_key=True)
    name = db.Column(db.String(100))
    qty = db.Column(db.Integer)

    def __init__(self, product_id, name, qty):
        self.product_id = product_id
        self.name = name
        self.qty = qty

class Location(db.Model):
    location_id = db.Column(db.String(100), primary_key=True)
    name = db.Column(db.String(100))

    def __init__(self, location_id, name):
        self.location_id = location_id
        self.name = name

class ProductMovement(db.Model):
    movement_id = db.Column(db.String(100), primary_key=True)
    timestamp = db.Column(db.String(100))
    from_location = db.Column(db.String(100), db.ForeignKey('location.location_id'))
    to_location = db.Column(db.String(100), db.ForeignKey('location.location_id'))
    product_id = db.Column(db.String(100), db.ForeignKey('product.product_id'))
    qty = db.Column(db.Integer)

    def __init__(self, movement_id, timestamp, from_location, to_location, product_id, qty):
        self.movement_id = movement_id
        self.timestamp = timestamp
        self.from_location = from_location
        self.to_location = to_location
        self.product_id = product_id
        self.qty = qty

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/add_product', methods=['GET', 'POST'])
def add_product():
    if request.method == 'POST':
        product_id = request.form['product_id']
        name = request.form['name']
        qty = request.form['qty']
        new_product = Product(product_id, name, qty)
        db.session.add(new_product)
        db.session.commit()
        return 'Product added successfully'
    return render_template('add_product.html')

@app.route('/add_location', methods=['GET', 'POST'])
def add_location():
    if request.method == 'POST':
        location_id = request.form['location_id']
        name = request.form['name']
