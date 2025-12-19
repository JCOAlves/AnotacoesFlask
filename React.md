# Imprementando React JS
Implementar o Flask com o React JS envolve usar o Flask como API de backend (servidor) e o React como frontend (interface de usuário). Essa é a arquitetura moderna conhecida como MVA (Model-View-Adapter) ou Single Page Application (SPA).

A chave para essa integração é garantir que o React possa fazer requisições HTTP (GET, POST, etc.) para a API Flask e que o Flask saiba como servir a aplicação React compilada.
Além disso, a estrutura do projeto deve ser organizada, e a maneira mais organizada é manter o frontend e o backend em pastas separadas:
```cmd
/projeto_completo
├── /backend/               # Servidor Flask (API)
│   ├── app.py
│   ├── /venv
│   └── ...
└── /frontend/              # Aplicação React (Interface)
    ├── package.json
    ├── /src
    └── ...
```

## 1. Configuração do Flask (Backend - API)
O Flask será responsável por hospedar e servir a API para o banco de dados. Com isso, defina rotas no Flask que retornem dados em formato JSON, utilizando jsonify.
```Python
# backend/app.py
from flask import Flask, jsonify
from flask_cors import CORS # Necessário para desenvolvimento

app = Flask(__name__)
CORS(app) # Habilita o CORS para que o React (em outra porta) possa acessar

@app.route('/api/status')
def get_status():
    return jsonify({
        'status': 'Online', 
        'version': '1.0'
    })

@app.route('/api/dados_usuario')
def get_user_data():
    # Aqui você faria a busca no banco de dados
    return jsonify([
        {'id': 1, 'nome': 'Alice'},
        {'id': 2, 'nome': 'Bob'}
    ])

if __name__ == '__main__':
    app.run(debug=True, port=5000) # Flask rodando na porta 5000
```

Durante a fase de desenvolvimento, o React geralmente roda na porta 3000 (ou similar) e o Flask na porta 5000. Isso é considerado uma requisição de domínio cruzado (Cross-Origin Request). Para evitar erros de segurança no navegador, você precisa instalar e usar o `Flask-CORS`:

```Bash
pip install flask-cors
```
Existem duas formas principais de configurar o CORS no seu aplicativo Flask:

1. Para Todas as Rotas (Recomendado na Fase de Desenvolvimento):
   Esta é a maneira mais simples, onde o CORS é habilitado para todas as rotas da sua aplicação, aceitando requisições de qualquer origem (origins='*'). É ideal para o desenvolvimento local.

    ```Python

    # backend/app.py
    from flask import Flask
    from flask_cors import CORS # 1. Importar CORS

    app = Flask(__name__)

    # 2. Inicializar CORS, permitindo requisições de qualquer origem ('*')
    CORS(app) 

    # Rotas da API
    @app.route('/api/status')
    def get_status():
    # ...
    ```
2. Para Origens Específicas (Recomendado para Produção):
    Para maior segurança, você deve restringir as requisições apenas aos domínios (origens) confiáveis. Durante o desenvolvimento, você pode listar a porta do seu React.

    ```Python
    # backend/app.py
    from flask import Flask
    from flask_cors import CORS

    app = Flask(__name__)

    # Configuração que permite apenas requisições da porta 3000 (React local)
    CORS(app, origins='http://localhost:3000') 

    # Se você tivesse mais de um domínio permitido:
    # CORS(app, origins=['http://localhost:3000', 'https://seusite.com.br']) 

    # ... Rotas da API
    ```
3. Para Rotas Individuais:
    Se você precisar de controle granular, pode usar o decorador cross_origin() em rotas específicas:

    ```Python
    from flask import Flask, jsonify
    from flask_cors import cross_origin

    app = Flask(__name__)

    # Esta rota só permite acesso da porta 3000
    @app.route('/api/dados_protegidos')
    @cross_origin(origins='http://localhost:3000') 
    def dados_protegidos():
        return jsonify({"dados": "confidenciais"})

    # Esta rota permite acesso de qualquer origem
    @app.route('/api/dados_publicos')
    @cross_origin() 
    def dados_publicos():
        return jsonify({"dados": "publicos"})
    ```
O método 1 é o mais prático para começar, mas lembre-se de restringir as origens na etapa de produção usando o método 2.

## 2. Configuração do React (Frontend - Interface)
O React será responsável por renderizar a interface e fazer as requisições HTTP. Ele usará a função useEffect para chamar a API Flask assim que o componente for montado.
```JavaScript
// frontend/src/components/UserList.js (Exemplo de componente React)
import React, { useState, useEffect } from 'react';
import axios from 'axios'; // Usando a biblioteca Axios, muito comum

function UserList() {
  const [data, setData] = useState([]);
  
  useEffect(() => {
    // Acessa o endpoint Flask na porta 5000
    axios.get('http://127.0.0.1:5000/api/dados_usuario')
      .then(response => {
        setData(response.data);
      })
      .catch(error => {
        console.error("Erro ao buscar dados do Flask:", error);
      });
  }, []);

  return (
    <div>
      <h1>Lista de Usuários (via Flask)</h1>
      <ul>
        {data.map(user => (
          <li key={user.id}>{user.nome}</li>
        ))}
      </ul>
    </div>
  );
}
export default UserList;
```

## 3. Envio e recebimento de cookies do sessão
Segundo a política do CORS, o navegador por padrão não envia cookies (onde a sessão do Flask é armazenada) 
em requisições feitas de um domínio/porta diferente, a menos que você o instrua explicitamente. 
Dessa forma, devemos configurar asn credenciais do React e do Flask para o envio de cookies.

- Configurar Credenciais no React
  Você deve dizer à função fetch para incluir os cookies na requisição, definindo a opção credentials como 'include'. 
  ```javascript
  //Metodo GET
    export async function GET(rota){ 
        try { 
            let resposta = await fetch(rota, {credentials: 'include'}); // Permite que o navegador pegue cookies de sessão
            const dados = await resposta.json();
            if("mensagemServidor" in dados){
                return dados["mensagemServidor"];
            }else{
                return dados;
            }

        } catch (erro) {
            console.error("Erro na busca de dados", erro)
            return {"mensagemServidor": `Erro na busca de dados: ${erro.message || erro}`};
        }
    };

    //Metodo POST
    export async function POST(rota, objeto) {
        try {
            const objetoJSON = JSON.stringify(objeto);
            let resposta = await fetch(rota, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: objetoJSON,
                credentials: 'include' // Permite que o navegador envie cookies da sessão
            })
            resposta = await resposta.json();
            return resposta
        }
        catch {
            console.error("Erro no envio de dados", erro)
            return {"mensagemServidor": `Erro no envio de dados: ${erro.message || erro}`}
        }
    };

    //Metodo PUT
    export async function PUT(rotaEspecifica, objeto) {
        try{
            const objetoJSON = JSON.stringify(objeto);
            let resposta = await fetch(rotaEspecifica, {
                method: 'PUT',
                headers: { 'Content-Type': 'application/json' },
                body: objetoJSON,
                credentials: 'include' // Permite que o navegador envie cookies da sessão
            })
            resposta = await resposta.json();
            return resposta
        }
        catch{
            console.error("Erro na atualização de dados", erro)
            return {"mensagemServidor": `Erro na atualização de dados: ${erro.message || erro}`};
        }
    };

    //Metedo DELETE
    export async function DELETE(rotaEspecifica) {
        try{
            let resposta = await fetch(rotaEspecifica, {
                method: 'DELETE',
                headers: { 'Content-Type': 'application/json' },
                credentials: 'include' // Permite que o navegador envie cookies da sessão
            })
            resposta = await resposta.json();
            return resposta
        }
        catch{
            console.error("Erro na excluição de dados", erro)
            return {"mensagemServidor": `Erro na excluição de dados: ${erro.message || erro}`};
        }
    };
  ```
- Configurar Credenciais no Flask-CORS
  Você também precisa dizer ao Flask para aceitar essas credenciais de cross-origin. No seu app.py (Flask):
  ```python
  from flask_cors import CORS

  # ...
  # CORREÇÃO CRÍTICA: Permite que o CORS envie cookies de volta
  CORS(app, supports_credentials=True, origins="http://localhost:3000") # Use a porta correta do seu React

  app.config['SECRET_KEY'] = 'SUA_CHAVE_SECRETA_MUITO_LONGA_E_RANDOMICA'
  app.config['SESSION_COOKIE_SAMESITE'] = 'Lax' # Ou None se precisar de compatibilidade total, mas Lax é mais seguro
  # ...
  ```

## 4. Etapa de Produção (Deployment)
Para a produção (quando você for hospedar o site final), você não pode rodar o React e o Flask em portas separadas. Você usará o Flask para servir os arquivos estáticos do React.
Compilar o React: No terminal da pasta /frontend, execute o comando de build do React:
```Bash
npm run build
```

Isso criará uma pasta `build/` (ou dist/) contendo todos os arquivos HTML, CSS e JavaScript estáticos e otimizados do seu frontend.
Configurar o Flask para Servir o Build: Mova a pasta `build/` do React para dentro da pasta backend/static/ (ou renomeie o parâmetro static_folder do Flask).
Em seguida, configure o Flask para capturar todas as rotas não-API e enviar o arquivo index.html do React:

```Python
# backend/app.py (Configuração de produção)
import os
from flask import send_from_directory, Flask

app = Flask(__name__, static_folder='../frontend/build', static_url_path='/')

# Rota API (retorna JSON)
@app.route('/api/status')
def get_status():
    # ... retorna JSON ...

# Rota Padrão (rotas de frontend - Single Page Application)
@app.route('/', defaults={'path': ''})
@app.route('/<path:path>')
def serve(path):
    if path != "" and os.path.exists(app.static_folder + '/' + path):
        return send_from_directory(app.static_folder, path)
    else:
        # Envia o arquivo index.html principal do React para rotas não encontradas
        return send_from_directory(app.static_folder, 'index.html')

# Rodar o Flask na porta 5000 (servirá tanto a API quanto o frontend)
# ...
```
Dessa forma, o Flask atua como o servidor unificado que entrega o código da interface (React) e processa as solicitações da API.
