# Implementado banco de dados SQL
Um banco de dados é uma coleção organizada de informações inter-relacionadas, armazenadas eletronicamente num sistema de computador para facilitar a gestão, o acesso e a recuperação de dados.

Para conectar seu projeto Flask a um banco de dados SQL, a maneira mais comum e recomendada é usar uma biblioteca de mapeamento objeto-relacional (ORM). O **SQLAlchemy** é a escolha padrão na comunidade Python/Flask. O SQLAlchemy permite que você trabalhe com o banco de dados usando objetos Python em vez de escrever comandos SQL brutos. Isso torna seu código mais limpo, seguro e fácil de manter.

## 1. Instalação das bibliotecas necessárias

Você precisará do `Flask-SQLAlchemy` (uma extensão do Flask que integra o SQLAlchemy) e do driver do banco de dados que você vai usar.

Para um banco de dados **SQLite** (ótimo para desenvolvimento), instale:

```bash
pip install Flask-SQLAlchemy
```

Para **PostgreSQL**, use:

```bash
pip install Flask-SQLAlchemy psycopg2-binary
```

Para **MySQL**, use:

```bash
pip install Flask-SQLAlchemy PyMySQL
```

## 2.  Configure a aplicação Flask

No seu arquivo `app.py`, importe e configure a extensão `Flask-SQLAlchemy`.

```bash
# app.py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

# Configura a URI do banco de dados.
# Para SQLite, o arquivo do banco de dados será 'site.db' no mesmo diretório.
# app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///site.db'

# Para MySQL o formato é: 'mysql+pymysql://<user>:<password>@<host>:<port>/<db_name>'
app.config['SQLALCHEMY_DATABASE_URI'] = 'mysql+pymysql://usuario:senha@localhost:3306/nome_do_banco'

# Define para False para evitar um aviso no console.
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

# Cria uma instância do banco de dados.
db = SQLAlchemy(app)
```

- `user`: Nome de usuário do seu banco de dados MySQL.
- `password`: Senha do seu usuário.
- `host`: Endereço do servidor e porta do MySQL. Se o MySQL estiver rodando na mesma máquina, `localhost` é o padrão.
- `port`: Porta logica da aplicação. A porta padrão do MySQL é `3306`.
- `db_name`: O nome do banco de dados que você já criou no MySQL.