FLASK APP TRYING TO MAKE A SQL DATABSE SO TINY

THIS IS MASCOTAS.PY -------------------------------------------------------
import os
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

directorio = os.path.abspath(os.path.dirname(__file__))

app = Flask(__name__)

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///' + os.path.join(directorio, 'datos2.sqlite')
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

basededatos = SQLAlchemy(app)
Migrate(app, basededatos)

class Mascota(basededatos.Model):
    __tablename__ = 'pets'
    id = basededatos.Column(basededatos.Integer, primary_key=True)
    nombre = basededatos.Column(basededatos.Text)
    juguetes = basededatos.relationship('Juguete', backref='mascota', uselist=False)
    propietario = basededatos.relationship('Propietario', backref='mascota', uselist=False)

    def __init__(self, nombre):
        self.nombre = nombre

    def __repr__(self):
        Texto = "Mascota: nombre {}".format(self.nombre)
        return Texto

    def mostrar_juguetes(self):
        for i in self.juguetes:
            print(i.nombre)

class Juguete(basededatos.Model):
    __tablename__ = 'juguetes'
    id = basededatos.Column(basededatos.Integer, primary_key=True)
    nombre = basededatos.Column(basededatos.Text)
    mascota_id = basededatos.Column(basededatos.Integer, basededatos.ForeignKey('pets.id'))

    def __init__(self, nombre, mascota_id):
        self.nombre = nombre
        self.mascota_id = mascota_id

class Propietario(basededatos.Model):
    __tablename__ = 'propietarios'
    id = basededatos.Column(basededatos.Integer, primary_key=True)
    nombre = basededatos.Column(basededatos.Text)
    mascota_id = basededatos.Column(basededatos.Integer, basededatos.ForeignKey('pets.id'))

    def __init__(self, nombre, mascota_id):
        self.nombre = nombre
        self.mascota_id = mascota_id

THIS IS CONSULTAS.PY -------------------------------------------------------------------------

from Mascotas import basededatos, Propietario, Juguete, Mascota, app
with app.app_context():
    #creaos las mascotas
    mascota1 = Mascota.query.filter_by(nombre='led').first()

    prop1 = Propietario("Pedro", mascota1.id)
    basededatos.session.add(prop1)
    basededatos.session.commit()

    juguete1 = Juguete("Pelotita", mascota1.id)
    juguete2 = Juguete("hueso", mascota1.id)
    basededatos.session.add_all([juguete1, juguete2])
    basededatos.session.commit()


    todas = Mascota.query.all()
    print(todas)

    mascota1 = Mascota.query.filter_by(nombre="led").first()
    print(mascota1)

with app.app_context():
    basededatos.create_all()
--------------------------------------------------------------------------------------------------------
I HAVE AN ERROR THAT SAYS THERE IS NO tablename created as 'pets'
SO IM SUPPOSE TO DO LIKE:
set FLAS_APP=Consultas.py
flask db init
flask db migrate
flask db upgrade
in order to create the table, 
BUT IT DOENST WORK CAUSE ITS SAYS:
NO SUCH COMMAND AS 'db'
so im in stock
