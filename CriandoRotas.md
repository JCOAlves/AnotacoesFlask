# Criando Rotas Adicionais
Você pode criar múltiplas rotas em sua aplicação Flask.

**Modifique `app.py` para incluir uma nova rota:**

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Olá, Mundo!'

@app.route('/sobre')
def sobre():
    return 'Esta é a página Sobre.'

if __name__ == '__main__':
    app.run(debug=True)
```

Salve o arquivo. Como o `debug=True` está ativado, o servidor será reiniciado automaticamente.

Agora, acesse `http://127.0.0.1:5000/sobre` no seu navegador para ver a nova página.