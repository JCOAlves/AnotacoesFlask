# Executando a Aplicação Flask
Com o ambiente virtual ativado e o arquivo `app.py` salvo, execute sua aplicação:

1. **Abra o terminal/prompt de comando** (certifique-se de que o ambiente virtual está ativado).
2. **Navegue até o diretório do seu projeto** (onde `app.py` está).
3. **Execute o script:**
    
    ```powershell
    python app.py
    #Testar flask run depois
    ```
    

Você verá uma saída similar a esta no seu terminal:

```powershell
 * Serving Flask app 'app'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:5000
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 123-456-789
 
 #Caso você queira encerra o ambiente vitual, utilize o comando **exit**
```

Agora, abra seu navegador web e acesse `http://127.0.0.1:5000`. Você deverá ver "Olá, Mundo!" na tela.