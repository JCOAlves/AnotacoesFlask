# Usando Variáveis nas Rotas

Usar **variáveis em rotas Flask** é uma funcionalidade poderosa que permite criar URLs dinâmicas e flexíveis na sua aplicação. Em vez de ter uma rota separada para cada item (por exemplo, `/produtos/1`, `/produtos/2`, `/produtos/3`), você pode ter uma única rota `/produtos/<id>` que captura o ID como uma variável.

## 1. Como Declarar Variáveis em Rotas

Você declara variáveis na sua rota usando `<nome_da_variavel>`. O Flask automaticamente passa o valor capturado dessa parte da URL como um argumento para a função de visualização associada.

**Exemplo Básico:**

```python
from flask import Flask

app = Flask(__name__)

@app.route('/usuarios/<nome_usuario>')
def perfil_usuario(nome_usuario):
    return f'Olá, {nome_usuario}! Bem-vindo(a) ao seu perfil.'

if __name__ == '__main__':
    app.run(debug=True)
```

Neste exemplo:

- Se você acessar `http://127.0.0.1:5000/usuarios/alice`, a função `perfil_usuario` será chamada com `nome_usuario='alice'`.
- Se acessar `http://127.0.0.1:5000/usuarios/bob`, a função será chamada com `nome_usuario='bob'`.

---

## 2.  Conversores de Variáveis (Tipos de Dados)

Por padrão, as variáveis capturadas são strings. No entanto, o Flask oferece **conversores** que permitem especificar o tipo de dado que você espera para a variável na URL. Isso é útil para validação e para que o Python já receba o tipo correto (inteiro, float, etc.).

Você usa os conversores adicionando `:tipo` após o nome da variável: `<nome_da_variavel:tipo>`.

Aqui estão os conversores mais comuns:

- **`<string:variavel>`** (Padrão, não precisa ser especificado): Aceita qualquer texto sem barra (`/`).
    - Ex: `/artigos/<string:titulo>`
- **`<int:variavel>`**: Aceita apenas números inteiros.
    - Ex: `/produtos/<int:id>`
- **`<float:variavel>`**: Aceita números de ponto flutuante (decimais).
    - Ex: `/precos/<float:valor>`
- **`<path:variavel>`**: Aceita strings que podem incluir barras (`/`), útil para caminhos de arquivo ou URLs parciais.
    - Ex: `/arquivos/<path:caminho_arquivo>`
- **`<uuid:variavel>`**: Aceita UUIDs (Universal Unique Identifiers).
    - Ex: `/transacoes/<uuid:id_transacao>`

**Exemplos com Conversores:**

```python
from flask import Flask

app = Flask(__name__)

# Rota com um ID de produto (inteiro)
@app.route('/produto/<int:id_produto>')
def ver_produto(id_produto):
    # Flask já garante que id_produto é um int
    return f'Detalhes do produto com ID: {id_produto}'

# Rota para um caminho de arquivo (pode conter '/')
@app.route('/download/<path:caminho_arquivo>')
def baixar_arquivo(caminho_arquivo):
    # caminho_arquivo pode ser algo como 'documentos/relatorio.pdf'
    return f'Preparando para baixar o arquivo: {caminho_arquivo}'

# Rota com um preço (float)
@app.route('/desconto/<float:percentual>')
def aplicar_desconto(percentual):
    return f'Aplicando {percentual}% de desconto.'

if __name__ == '__main__':
    app.run(debug=True)
```

**Testando no Navegador:**

- `http://127.0.0.1:5000/produto/123` -> `Detalhes do produto com ID: 123`
- `http://127.0.0.1:5000/produto/abc` -> Retornará um erro 404 Not Found, pois 'abc' não é um inteiro.
- `http://127.0.0.1:5000/download/pasta/subpasta/arquivo.txt` -> `Preparando para baixar o arquivo: pasta/subpasta/arquivo.txt`
- `http://127.0.0.1:5000/desconto/10.5` -> `Aplicando 10.5% de desconto.`

## 3. Múltiplas Variáveis em uma Rota

Você pode ter quantas variáveis precisar em uma única rota, contanto que seus nomes sejam diferentes.

```python
from flask import Flask

app = Flask(__name__)

@app.route('/posts/<int:ano>/<int:mes>/<slug>')
def post_detalhes(ano, mes, slug):
    return f'Post do ano {ano}, mês {mes}, slug: {slug}'

if __name__ == '__main__':
    app.run(debug=True)
```

Exemplo: `http://127.0.0.1:5000/posts/2023/07/meu-primeiro-post`

## 4. Usando `url_for()` com Variáveis

Quando você cria links ou redirecionamentos para rotas com variáveis, você deve usar a função `url_for()`, passando os valores das variáveis como argumentos de palavra-chave.

```python
from flask import Flask, url_for, redirect

app = Flask(__name__)

@app.route('/')
def index():
    # Gerando um link para a rota 'ver_produto' com id_produto=5
    link_produto = url_for('ver_produto', id_produto=5)
    return f'Clique <a href="{link_produto}">aqui</a> para ver o produto 5.'

@app.route('/produto/<int:id_produto>')
def ver_produto(id_produto):
    return f'Detalhes do produto com ID: {id_produto}'

@app.route('/redirecionar-para-produto/<int:id>')
def redirecionar_produto(id):
    # Redirecionando para a rota 'ver_produto' com o ID fornecido
    return redirect(url_for('ver_produto', id_produto=id))

if __name__ == '__main__':
    app.run(debug=True)
```

- Acessando `http://127.0.0.1:5000/` vai mostrar um link para `/produto/5`.
- Acessando `http://127.0.0.1:5000/redirecionar-para-produto/10` vai te levar para `/produto/10`.

As variáveis de rota são uma parte essencial do Flask para criar URIs limpas, semânticas e flexíveis, permitindo que sua aplicação responda de forma diferente a URLs ligeiramente variadas.