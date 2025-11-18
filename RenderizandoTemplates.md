# Renderizando Templates (HTML)
Para criar aplicações web mais complexas, você precisará renderizar arquivos HTML. O Flask usa o **Jinja2** como seu motor de templates.

## 1. Criando a Pasta `templates`
O Flask utiliza a função `render_template()`, que por padrão procura arquivos na pasta `templates/`.

1. Crie uma pasta chamada `templates` no mesmo nível do seu arquivo `app.py`.
2. Dentro da pasta `templates`, crie um arquivo HTML chamado `index.html` com o seguinte conteúdo:
    ```html
    <!DOCTYPE html>
    <html lang="pt-br">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Minha Aplicação Flask</title>
    </head>
    <body>
        <h1>Bem-vindo ao Flask!</h1>
        <p>Esta é uma página renderizada por um template.</p>
        <p>Olá, {{ nome }}!</p>
    </body>
    </html>
    ```
    Observe o `{{ nome }}`. Isso é sintaxe Jinja2 para exibir uma variável passada para o template.

Mas caso queiramos renderizar um arquivo HTML que não esteja na pasta templates, você tem duas abordagens principais:

1. Modificar o parâmetro `template_folder` ao criar sua instância do aplicativo Flask:
    ```python
    from flask import Flask, render_template

    # 1. Definindo o novo caminho relativo
    NOVA_PASTA_TEMPLATES = 'views'

    # 2. Passando o argumento template_folder
    app = Flask(__name__, template_folder=NOVA_PASTA_TEMPLATES) 

    # Estrutura do projeto:
    # /app.py
    # /views/
    #   /index.html
    #   /outra_pagina.html

    @app.route('/')
    def index():
        # O Flask agora procurará por 'index.html' dentro de 'views/'
        return render_template('index.html', title='Página Principal')

    if __name__ == '__main__':
        app.run(debug=True)
    ```
    Essa é a solução Flask nativa. Todas as suas chamadas futuras a `render_template('...')` usarão automaticamente o novo diretório `views/`.

2. Se você precisar renderizar um arquivo HTML de um local esporádico ou apenas servir o conteúdo estático de um arquivo HTML sem o poder de renderização do Jinja (ou seja, sem variáveis, loops ou includes), você pode usar a função `send_file()`:
    ```python
    from flask import Flask, send_file
    import os

    app = Flask(__name__)

    # O Flask precisa do caminho absoluto
    CAMINHO_RAIZ = os.path.dirname(os.path.abspath(__file__)) 
    CAMINHO_DO_ARQUIVO = os.path.join(CAMINHO_RAIZ, 'minha_pagina_na_raiz.html')

    @app.route('/pagina_raiz')
    def pagina_raiz():
        try:
            # A função send_file() envia o arquivo diretamente
            return send_file(CAMINHO_DO_ARQUIVO)
        except FileNotFoundError:
            return "Arquivo não encontrado.", 404

    if __name__ == '__main__':
        app.run(debug=True)
    ```
    É mais trabalhoso, pois exige que você defina o caminho completo do arquivo. Além disso, você perde a capacidade de usar o Jinja, o que significa que não pode injetar dados dinâmicos do backend nesse HTML.

## 2. Modificando `app.py` para Renderizar o Template
```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def index():
    return render_template('index.html', nome='Usuário') # Passa a variável 'nome' para o template

@app.route('/sobre')
def sobre():
    return 'Esta é a página Sobre.'

@app.route('/usuario/<nome>')
def saudacao_usuario(nome):
    return f'Olá, {nome}!'

if __name__ == '__main__':
    app.run(debug=True)
```

Salve ambos os arquivos. Agora, ao acessar `http://127.0.0.1:5000`, você verá o conteúdo do `index.html` e a variável `nome` será substituída por "Usuário".
