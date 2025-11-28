# Criando um projeto Flask

## 1. Criando um Ambiente Virtual 
Antes de instalar o Flask, é altamente recomendável criar um ambiente virtual. Isso isola as dependências do seu projeto, evitando conflitos com outros projetos Python no seu sistema.

1. Abra o terminal ou prompt de comando.
2. Navegue até o diretório onde você quer criar seu projeto.
    
    ```bash
    cd /caminho/para/seu/projeto
    ```
    
3. Crie o ambiente virtual:
    ```bash
    python -m venv .venv
    ```
    (`.venv` é o nome do seu ambiente virtual. Você pode escolher outro nome se preferir).

## 2. Ativando o Ambiente Virtual
Após criar o ambiente virtual, você precisa ativá-lo:

- **No Windows:**
    ```cmd
    .venv\Scripts\activate
    ```
    
- **No macOS/Linux:**
    ```bash
    source .venv/bin/activate
    ```
    

Você saberá que o ambiente virtual está ativo quando o nome do ambiente (por exemplo, `(.venv)`) aparecer no início da linha de comando.

## 3. Instalando o Flask
Com o ambiente virtual ativado, você pode instalar o Flask usando `pip`:
```bash
pip install Flask
```

Para saber se o Flask foi instalado com sucessso, utilize os comandos: `pip show Flask` ou `Flask —version`

## 4. Executando a Aplicação Flask
Com o ambiente virtual ativado e o arquivo `app.py` salvo, execute sua aplicação:

1. Abra o terminal/prompt de comando (certifique-se de que o ambiente virtual está ativado).
2. Navegue até o diretório do seu projeto (onde `app.py` está).
3. Execute o script:
    ```bash
    python app.py
    ```
    
Você verá uma saída similar a esta no seu terminal:
```bash
 * Serving Flask app 'app'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:5000
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 123-456-789
```
Caso você queira encerra o ambiente vitual, utilize o comando `exit`.

Agora, abra seu navegador web e acesse `http://127.0.0.1:5000`. Você deverá ver "Olá, Mundo!" na tela.

## 5. Criando Sua Primeira Aplicação Flask
Agora que o Flask está instalado, vamos criar uma aplicação "Olá, Mundo!".

1. Crie um arquivo Python dentro do diretório do seu projeto (e fora do diretório `venv`). Vamos chamá-lo de `app.py`.
2. Adicione o seguinte código ao `app.py`:Python
    
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
