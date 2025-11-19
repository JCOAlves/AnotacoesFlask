# Manipulando banco de dados com o Flask

## 1. Criando modelos de tabelas
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
- ```Enum('dado1','dado2')```: Restrige a coluna para um grupo de valores especifico. Para ser usada precissa ser importada diretamente do SQLAlchemy.
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

### **CREATE (Criar)**: 
Crie uma nova instância do modelo, adicione-a à sessão do banco de dados e confirme as mudanças.
    
```python
novo_usuario = Usuario(nome="João")
db.session.add(novo_usuario)
db.session.commit()
```
No caso de os dados a serem registrados no banco seja do tipo date, é necessario converter strings de data/hora para objetos Python

```python
from datetime import datetime

# 1. Sua string de data e hora
data_hora_string = "2023-12-09 19:30:34"

# 2. O formato que corresponde à sua string
# %Y: Ano com 4 dígitos (2023)
# %m: Mês com 2 dígitos (12)
# %d: Dia com 2 dígitos (09)
# %H: Hora (24h) (19)
# %M: Minuto (30)
# %S: Segundo (34)
formato_da_string = "%Y-%m-%d %H:%M:%S"

# 3. Conversão
data_hora_objeto = datetime.strptime(data_hora_string, formato_da_string)

# Resultado: data_hora_objeto é um objeto datetime.datetime(2023, 12, 9, 19, 30, 34)
print(data_hora_objeto)
```
Já se queremos criar mais de um elemento em uma tabela por vez, podemos utilizar a função `db.session.add_all()` para adicionamos uma lista de registros em uma tabela:
```python
# 1. Crie ou carregue as instâncias do modelo
informativo = Informativos(assunto="Novo Assunto", mensagem="Nova mensagem")
relacionamento = Turma_informativo(turma=101, informativo=None) # O ID será preenchido
dados_adicionais = Dados_avaliacoes(tipoAvaliacao="Prova", ...)

# 2. Reúna todos os objetos em uma lista
objetos_para_salvar = [informativo, relacionamento, dados_adicionais]

# 3. Adicione todos à sessão
db.session.add_all(objetos_para_salvar)

# 4. Finalize a transação (salva tudo no banco de dados)
db.session.commit()
```
    
### **READ (Ler)** 
Use o método `.query` para buscar registros.

#### Busca por Chave Primária (Primary Key)
  Essas funções são ideais quando você sabe o ID exato que está procurando. Elas buscam apenas um único objeto e são extremamente otimizadas.
    - ```.get(pk)```: A maneira mais simples de buscar um objeto pela sua chave primária (PK). Retorna o Objeto (instância da classe) ou	None (o mais seguro para verificar).
    - ```.get_or_404(pk)```: Semelhante a ```.get()```, mas é uma conveniência do Flask-SQLAlchemy (ou Flask) para lidar com rotas. Retorna o objeto ou levanta um erro HTTP 404 (Not Found), interrompendo a execução.
      
        ```python
        # Busca um único informativo pelo ID 5. Se não existir, retorna None.
        info = Informativos.query.get(5)
        ```
#### Consulta e Filtragem (Querying and Filtering)
Essas são as funções mais comuns para buscar dados com base em condições, não apenas pela chave primária.

- ```.filter_by(kwargs)```: Aplica filtros baseados em palavras-chave (atributos da classe). É o método mais simples.
- ```.filter(*args)```: Aplica filtros usando expressões SQL mais complexas, como comparações (>, !=), like, and_, or_. 
- ```.all()```: Executa a consulta e retorna todos os resultados correspondentes como uma lista de objetos.
- ```.first()``` Executa a consulta e retorna o primeiro objeto encontrado. Útil quando você espera, no máximo, um resultado.
- ```.one()```: Executa a consulta e espera exatamente um resultado.
- ```.one_or_none()```: Executa a consulta e espera um ou nenhum resultado.
  
```python
# Busca todos os informativos com 'assunto' igual a "Avaliação"
avaliacoes = Informativos.query.filter_by(assunto="Avaliação").all()

# Busca o primeiro material didático para o ID_informativo 10
material = Dados_materiais.query.filter_by(informativo=10).first()

# Usando .filter() para expressões mais complexas (ex: data maior que)
# (Você precisaria importar 'db.func' ou 'and_', 'or_' do sqlalchemy)
eventos_futuros = Dados_eventos.query.filter(Dados_eventos.data_InicioEvento > data_hoje).all()
```
#### Contagem, Ordem e Limitação
Usadas para refinar o dataset ou obter estatísticas sobre os dados.

- ```.count()```:	Retorna o número de resultados que a consulta produziria. (Obsoleto, prefira ```db.session.query(Model).count()```). Retorna um número inteiro.
- ```.order_by(*args)```:	Ordena os resultados com base em uma coluna. Use ```.desc()``` para ordem decrescente.
- ```.limit(N)```: Limita o número máximo de resultados retornados.
  
```python
# 1. Busca todos os eventos, ordenados pela data inicial de forma decrescente
eventos_ordenados = Dados_eventos.query.order_by(Dados_eventos.data_InicioEvento.desc()).all()

# 2. Conta quantos informativos existem
total_infos = Informativos.query.count()
```
    
### **UPDATE (Atualizar)**
Encontre o registro que deseja modificar, altere os atributos do objeto e confirme as mudanças.
    
```python
    usuario = Usuario.query.get(1)
    usuario.nome = "Pedro"
    db.session.commit()
    
#No caso em que o registro que se deseje modificar seja date, é necessario converte-lo para objeto Python, como visto anteriomente.

# O NOVO VALOR que você deseja aplicar 
nova_data_string = "2026-05-15 14:00:00" 
formato = "%Y-%m-%d %H:%M:%S" # Adapte o formato se necessário

# CONVERSÃO: O SQLAlchemy só aceita o objeto nativo do Python
nova_data_objeto = datetime.strptime(nova_data_string, formato)
```
    
### **DELETE (Excluir)**
Encontre o registro, use `db.session.delete()` e confirme.
```python
#Busca o objeto a ser deletado.
usuario = Usuario.query.get(1)
db.session.delete(usuario)
db.session.commit()
```
