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

**Tipos de Dados (Types)**

Esses são os tipos de dados básicos que você usa para definir o tipo de informação que a coluna irá armazenar.

- ```db.Integer```: Um número inteiro.
- ```db.String(tamanho)```: Um texto de tamanho limitado. tamanho é obrigatório.
- ```db.Text```: Um texto longo, sem limite de tamanho.
- ```db.Boolean```: Um valor booleano (True ou False).
- ```db.Float```: Um número de ponto flutuante (decimal).
- ```db.DateTime```: Um valor de data e hora. Você pode usar default=db.func.now() para preencher automaticamente com a data e hora atuais.
- ```db.Date```: Um valor de data.
- ```db.Time```: Um valor de hora.
  
**Restrições (Constraints)**

Esses atributos de palavra-chave são usados dentro de db.Column() para adicionar restrições à coluna.
- ```primary_key=True```: Define a coluna como a chave primária da tabela. É usada para identificar de forma única cada linha.
- ```nullable=False```: Torna a coluna obrigatória. Um valor não pode ser ```NULL```.
- ```unique=True```: Garante que todos os valores na coluna sejam únicos. Isso impede a inserção de valores duplicados.
- ```default='valor'```: Define um valor padrão para a coluna. Se um valor não for fornecido na criação de um novo registro, este valor será usado.
- ```Enum('dado1','dado2')```: Restrige a coluna para um grupo de valores especifico.
- ```name='status_tarefa'```: É opcional, mas é uma boa prática para dar um nome explícito ao tipo ```ENUM``` no banco de dados.
- ```index=True```: Cria um índice na coluna, o que pode acelerar as operações de busca e consulta.
- ```db.func.now()```: Função que adiciona data e hora atuais no momento do registro

**Relacionamentos (Relationships)**

Esses atributos são usados para criar relacionamentos entre as tabelas do banco de dados, como chaves estrangeiras.

- ```db.ForeignKey('nome_da_tabela.nome_da_coluna')```: Define uma chave estrangeira. Ela vincula uma coluna a uma coluna de outra tabela. O nome da tabela deve ser todo em letras minúsculas.
- ```db.relationship()```: É usado na classe do modelo para definir a relação entre as tabelas. Ele não cria uma coluna no banco de dados, mas sim um atributo na classe Python que facilita o acesso aos dados relacionados. Por exemplo, em um modelo Usuario, você poderia definir um relacionamento para acessar todos os avisos criados por aquele usuário.

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
