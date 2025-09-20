# Renderizando Templates (HTML)

Para criar aplicações web mais complexas, você precisará renderizar arquivos HTML. O Flask usa o Jinja2 como seu motor de templates.

# 1. Criando a Pasta `templates`

O Flask espera que seus arquivos HTML estejam em uma pasta chamada `templates` dentro do diretório do seu projeto.

1. **Crie uma pasta chamada `templates`** no mesmo nível do seu arquivo `app.py`.
2. **Dentro da pasta `templates`, crie um arquivo HTML** chamado `index.html` com o seguinte conteúdo:
    
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
    

## 2. Modificando `app.py` para Renderizar o Template

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def index():return render_template('index.html', nome='Usuário') # Passa a variável 'nome' para o template

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