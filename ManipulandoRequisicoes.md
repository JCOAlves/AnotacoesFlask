# Manipulando Requisições
O Flask, por padrão, utiliza o método `GET` para as requisições. Para manipular outros métodos como `POST`, `PUT` ou `DELETE`, você precisa configurá-los explicitamente nas suas rotas.
A chave para isso é o parâmetro `methods` no decorador `@app.route()`.

## 1. Explicando os Métodos de Requisição
Cada método HTTP tem um propósito semântico específico. Usar o método correto torna a sua API mais clara e fácil de entender.

- **`GET`**: Usado para **buscar** ou **recuperar** dados do servidor. O `GET` não deve modificar os dados no servidor. É um método "seguro" e "idempotente" (fazer a mesma requisição várias vezes não tem efeitos colaterais).
    - **Exemplo**: Obter a lista de usuários, ver os detalhes de um produto.
- **`POST`**: Usado para **enviar** novos dados para o servidor. O `POST` cria um novo recurso. Não é idempotente (enviar a mesma requisição várias vezes pode criar vários recursos idênticos).
    - **Exemplo**: Cadastrar um novo usuário, enviar um formulário de contato.
- **`PUT`**: Usado para **atualizar** um recurso existente. O `PUT` substitui completamente o recurso. É idempotente (enviar a mesma requisição várias vezes não tem efeitos colaterais, pois o recurso será sempre o mesmo no final).
    - **Exemplo**: Atualizar o perfil de um usuário, modificar os dados de um produto.
- **`DELETE`**: Usado para **deletar** um recurso específico do servidor. É idempotente (deletar um recurso que não existe não causa erro, e deletar um que existe várias vezes tem o mesmo efeito final).
    - **Exemplo**: Remover um usuário do sistema.


## 2. Como Manipular requisições no Flask
A manipulação de diferentes métodos de requisição é feita no seu script principal do Flask (`app.py`).
Você pode usar o mesmo decorador `@app.route()` para lidar com vários métodos. O Flask fornece o objeto `request` para que você possa verificar qual método HTTP foi usado.

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

# A rota /recurso aceita GET e POST
@app.route('/recurso', methods=['GET', 'POST'])
def handle_recurso():
    if request.method == 'POST':
        # Código para lidar com um POST
        data = request.get_json()  # Pega os dados JSON enviados pelo cliente
        return jsonify({"mensagem": "Dados criados com sucesso!", "data": data}), 201
    else: # Por padrão, será um GET
        # Código para lidar com um GET
        return jsonify({"mensagem": "Aqui estão os dados do recurso."})
```
Com isso, há varias funções e métodos para manipular o objeto `request`:

- `request.get_json()`: Usado para pegar dados enviados no formato JSON no corpo da requisição, muito comum em APIs.
- `request.form`: Usado para pegar dados de formulários HTML (enviados como `application/x-www-form-urlencoded`).
- `request.args`: Usado para pegar dados de parâmetros na URL (por exemplo, `?q=python`).
- `request.json`: Usado para  detectar automaticamente o `Content-Type: application/json` no cabeçalho da requisição e converte o corpo JSON em um dicionário Python.

Ao entender a finalidade de cada método HTTP e como manipulá-los no Flask com `methods=['...']` e o objeto `request`, você estará apto a criar APIs web completas e bem estruturadas.

## 3. Rotas `GET` e `POST`
O cenário mais frequente é ter uma rota para buscar dados (`GET`) e outra para criar dados (`POST`).

- **`GET`:**
    ```python
    # Rota para CRIAR um novo recurso
    @app.route('/recurso', methods=['POST'])
    def create_recurso():
        # Pega os dados do corpo da requisição POST
        dados = request.get_json()
        
        # Valida e processa os dados...
        if not dados or 'nome' not in dados:
            return jsonify({"erro": "Nome do recurso é obrigatório"}), 400
        
        # Salva o novo recurso no banco de dados, por exemplo
        novo_recurso = {"id": 1, "nome": dados['nome']}
        
        # Retorna uma resposta de sucesso
        return jsonify(novo_recurso), 201 # 201 Created
    ```
- **`POST`:**
    ```python
    # Rota para OBTER um recurso específico
    @app.route('/recurso/<int:recurso_id>', methods=['GET'])
    def get_recurso(recurso_id):
        # Procura o recurso com o ID fornecido
        # Exemplo: busca em um banco de dados ou lista
        recurso = proximo_banco_de_dados.get(recurso_id) 
        
        if not recurso:
            return jsonify({"erro": "Recurso não encontrado"}), 404
            
        return jsonify(recurso)
    ```

## 4. Rotas `PUT` e `DELETE`
Normalmente, `PUT` e `DELETE` operam em um recurso específico, que geralmente é identificado por um ID na URL.

- **`PUT`:**
    ```python
    # Rota para ATUALIZAR um recurso existente
    @app.route('/recurso/<int:recurso_id>', methods=['PUT'])
    def update_recurso(recurso_id):
        # Pega os dados para atualização do corpo da requisição
        dados_atualizacao = request.get_json()

        # Procura e atualiza o recurso
        recurso_existente = proximo_banco_de_dados.get(recurso_id)
        if not recurso_existente:
            return jsonify({"erro": "Recurso não encontrado"}), 404
        
        # Lógica de atualização
        recurso_existente.update(dados_atualizacao)
        
        return jsonify({"mensagem": f"Recurso {recurso_id} atualizado."})
    ```

- **`DELETE`:**
    ```python
    # Rota para DELETAR um recurso
    @app.route('/recurso/<int:recurso_id>', methods=['DELETE'])
    def delete_recurso(recurso_id):
        # Procura e deleta o recurso
        recurso_a_deletar = proximo_banco_de_dados.get(recurso_id)
        if not recurso_a_deletar:
            return jsonify({"erro": "Recurso não encontrado"}), 404
        
        # Lógica de exclusão
        proximo_banco_de_dados.delete(recurso_id)
        
        return jsonify({"mensagem": f"Recurso {recurso_id} deletado com sucesso."}), 204 # 204 No Content
    ```

