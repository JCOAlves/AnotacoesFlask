# Lidando com Arquivos Estáticos

Por padrão, o Flask procura seus arquivos estáticos (CSS, JS, imagens, etc.) em uma pasta chamada **`static`** dentro do diretório raiz da sua aplicação.

**Estrutura de Diretórios Recomendada:**

Para que o Flask encontre seus arquivos estáticos, organize-os assim:

```python
seu_projeto/
├── .venv/
├── app.py
├── templates/
│   └── index.html
└── static/
    ├── css/
    │   └── style.css
    ├── js/
    │   └── script.js
    └── img/
        └── logo.png
```

Dentro da pasta `static`, você pode criar subpastas para organizar melhor seus arquivos (ex: `css`, `js`, `img`).

---

## 1. Crie os Arquivos Estáticos

Dentro da pasta `static`:

- **`static/css/style.css**:CSS`
    
    ```python
    body {
        font-family: Arial, sans-serif;
        background-color: #f4f4f4;
        color: #333;
        margin: 20px;
    }
    
    h1 {
        color: #0056b3;
    }
    
    p {
        font-size: 1.1em;
    }
    ```
    
- **`static/js/script.js**:JavaScript`
    
    ```python
    document.addEventListener('DOMContentLoaded', function() {
        console.log('O JavaScript está funcionando!');
        alert('Bem-vindo à sua aplicação Flask!');
    });
    ```
    

## 2. Modifique Seu Template HTML (`templates/index.html`)
Agora, você precisa referenciar esses arquivos no seu HTML. Para isso, o Flask oferece a função `url_for()`, que é essencial para gerar URLs para arquivos estáticos.

**`templates/index.html`**:

```python
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0"><title>Minha Aplicação Flask com Estáticos</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
</head>
<body>
    <h1>Olá do Flask!</h1>
    <p>Esta é uma página com estilos CSS e JavaScript.</p>

    <script src="{{ url_for('static', filename='js/script.js') }}"></script>
</body>
</html>
```

**Explicação de `url_for()`:**

- `url_for('static', filename='css/style.css')`:
    - `'static'`: Indica que você está solicitando um arquivo da pasta de arquivos estáticos.
    - `filename='css/style.css'`: Especifica o caminho do arquivo **relativo à pasta `static`**. Se o arquivo estivesse diretamente em `static`, seria apenas `filename='style.css'`.
    
    O Flask, ao processar `{{ url_for(...) }}`, vai gerar a URL correta para o arquivo, algo como `/static/css/style.css`. Isso é muito útil porque se a estrutura do seu projeto ou a URL base mudar no futuro, o Flask ajustará automaticamente os caminhos, evitando links quebrados.
    

## 3. Modifique Seu Arquivo da Aplicação (`app.py`)

Certifique-se de que sua rota principal esteja renderizando o `index.html`.

**`app.py`**:

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def index():
    return render_template('index.html') # Não precisa passar 'nome' se não usar no index.html

if __name__ == '__main__':
    app.run(debug=True)
```

---

## 4. Execute Sua Aplicação

1. **Abra seu terminal** no diretório raiz do seu projeto (`seu_projeto/`).
2. **Ative seu ambiente virtual**:
    - `.\.venv\Scripts\activate` (PowerShell)
    - `venv\Scripts\activate` (CMD)
3. **Execute sua aplicação Flask**:
    
    ```python
    python app.py
    ```
    
4. **Acesse no navegador**: Abra `http://127.0.0.1:5000`.

Você deverá ver a página com o estilo CSS aplicado (fundo cinza claro, texto escuro, título azul) e um alerta JavaScript aparecerá quando a página carregar, confirmando que ambos os arquivos estáticos foram carregados e executados corretamente.