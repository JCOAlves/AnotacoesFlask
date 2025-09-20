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

## 3. Criando modelos de tabelas

Para acessar ou criar tabelas no banco de dados, você deve definir modelos de tabelas do banco. Para criar modelos de tabelas no Flask, você usa o **SQLAlchemy** para mapear suas tabelas do banco de dados para classes Python. Cada classe que você cria representa uma tabela, e cada atributo dentro da classe representa uma coluna.

**Passo a passo**

1. **Crie a classe do modelo:** Crie um novo arquivo (por exemplo, `models.py`) para armazenar suas classes de modelo. Essa prática ajuda a organizar seu projeto.
2. **Importe `db`:** Importe a instância do SQLAlchemy (`db`) que você criou no seu arquivo principal (`app.py`).
3. **Defina a classe da tabela:** Crie uma nova classe que herda de `db.Model`. O nome da classe será o nome da sua tabela no banco de dados (ex: `Usuario`). Se você quiser usar um nome de tabela diferente, defina o atributo `__tablename__`.
4. **Defina as colunas:** Dentro da classe, crie atributos que correspondam às colunas da tabela. Use a função `db.Column()`para definir o tipo de dado e as restrições de cada coluna.

**Exemplo de Modelo:**

```python
from app import db  # Importa a instância do banco de dados

class Usuario(db.Model):
    # __tablename__ = 'usuarios'  # Opcional: define um nome diferente para a tabela
    id = db.Column(db.Integer, primary_key=True)
    nome = db.Column(db.String(100), nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    senha = db.Column(db.String(60), nullable=False)

    def __repr__(self):
        return f"Usuario('{self.nome}', '{self.email}')"

class Aviso(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    titulo = db.Column(db.String(200), nullable=False)
    conteudo = db.Column(db.Text, nullable=False)
    data_criacao = db.Column(db.DateTime, nullable=False, default=db.func.now())

    # Chave estrangeira para associar o aviso a um usuário
    autor_id = db.Column(db.Integer, db.ForeignKey('usuario.id'), nullable=False)

    def __repr__(self):
        return f"Aviso('{self.titulo}', '{self.data_criacao}')"
```

Explicação dos Atributos`db.Column`

- `db.Integer`: Tipo de dado para números inteiros.
- `db.String(tamanho)`: Tipo de dado para textos curtos de tamanho fixo.
- `db.Text`: Tipo de dado para textos longos, sem limite de tamanho.
- `db.DateTime`: Tipo de dado para datas e horas.
- `primary_key=True`: Define a coluna como a chave primária da tabela.
- `nullable=False`: Impede que a coluna tenha um valor nulo, ou seja, ela é obrigatória.
- `unique=True`: Garante que não haverá valores duplicados nesta coluna.
- `default=db.func.now()`: Define um valor padrão, que neste caso é a data e hora atuais.
- `db.ForeignKey('tabela.coluna')`: Cria uma chave estrangeira que vincula esta tabela a outra.

## 4. Criando tabelas no banco

Depois de definir seus modelos, você precisa dizer ao Flask-SQLAlchemy para criar as tabelas no seu banco de dados. A forma mais segura de fazer isso é usando o shell do Flask.

1. No seu arquivo principal (`app.py`), importe seus modelos.
    
    ```python
    # app.py
    from flask import Flask
    from flask_sqlalchemy import SQLAlchemy
    from models import Usuario, Aviso # Importa seus modelos
    app = Flask(__name__)# ... configuração do banco de dados...
    db = SQLAlchemy(app)
    ```
    
2. Abra o terminal na pasta do seu projeto. Certifique-se de que seu ambiente virtual está ativado.
3. Execute o comando `flask shell`.
4. Dentro do shell interativo, execute os seguintes comandos:

```python
>>> from app import app, db
>>> with app.app_context():
...     db.create_all()
```

O comando `db.create_all()` irá inspecionar todos os modelos que herdam de `db.Model` e criará as tabelas correspondentes no seu banco de dados MySQL. Se as tabelas já existirem, este comando não fará nada.

## 5. Criado o CRUD do banco

Com a conexão estabelecida, você pode realizar as operações de CRUD nas suas rotas Flask.

- **CREATE (Criar)**: Crie uma nova instância do modelo, adicione-a à sessão do banco de dados e confirme as mudanças.
    
    ```python
    novo_usuario = Usuario(nome="João")
    db.session.add(novo_usuario)
    db.session.commit()
    ```
    
- **READ (Ler)**: Use o método `.query` para buscar registros. Você pode pegar todos (`.all()`) ou um único registro por ID (`.get_or_404()`).
    
    ```python
    # Lê todos os usuários
    usuarios = Usuario.query.all()
    # Lê um único usuário por ID
    usuario = Usuario.query.get_or_404(1)
    # Para buscas por colunas específicas
    usuarios_por_nome = Usuario.query.filter_by(nome='Maria').all()
    ```
    
- **UPDATE (Atualizar)**: Encontre o registro que deseja modificar, altere os atributos do objeto e confirme as mudanças.
    
    ```python
    usuario = Usuario.query.get(1)
    usuario.nome = "Pedro"
    db.session.commit()
    ```
    
- **DELETE (Excluir)**: Encontre o registro, use `db.session.delete()` e confirme.
```python
usuario = Usuario.query.get(1)
db.session.delete(usuario)
db.session.commit()
```