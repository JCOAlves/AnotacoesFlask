# Criando Sua Primeira Aplicação Flask
Agora que o Flask está instalado, vamos criar uma aplicação "Olá, Mundo!".

1. **Crie um arquivo Python** dentro do diretório do seu projeto (e fora do diretório `venv`). Vamos chamá-lo de `app.py`.
2. **Adicione o seguinte código** ao `app.py`:Python
    
    ```python
    from flask import Flask
    
    app = Flask(__name__)
    
    @app.route('/')
    def hello_world():
        return 'Olá, Mundo!'
    
    if __name__ == '__main__':
        app.run(debug=True)
    ```

**Entendendo o Código:**
- `from flask import Flask`: Importa a classe `Flask` do pacote `flask`.
- `app = Flask(__name__)`: Cria uma instância da aplicação Flask. `__name__` é uma variável especial do Python que recebe o nome do módulo atual. O Flask a utiliza para determinar a raiz da aplicação.
- `@app.route('/')`: É um decorador que associa a função `hello_world()` à URL raiz (`/`). Quando alguém acessa a URL raiz do seu servidor, esta função será executada.
- `def hello_world():`: A função que será executada quando a rota `/` for acessada. Ela retorna a string "Olá, Mundo!".
- `if __name__ == '__main__':`: Garante que o servidor de desenvolvimento seja executado apenas quando o script for executado diretamente (não quando importado como um módulo).
- `app.run(debug=True)`: Inicia o servidor de desenvolvimento do Flask.
- `debug=True`: Ativa o modo de depuração. Isso fornece mensagens de erro detalhadas no navegador e recarrega o servidor automaticamente quando você faz alterações no código. **Não use `debug=True` em produção.**