# Configurando Nodejs
- npm install cfonts
- criando um arquivo index.js
    - "(
        console.log('Node rodando')
    )"

- no terminal
    - "(
        node index
    )"

# Configurando Express
- npm install express --save
- npm init -y
- Verificando express e iniciando a Api na porta 3000
    - "(
        const express = require('express')
        const app = express()
        const port = 3000

        app.listen(port, ()=> console.log('Api rodando na porta'+port))
    )"
- node index    

# Hello Word with Express
- app.get('/', (req, res)=> res.send('Hello World'))

# Metodos principais para criar um endpoint para users
- Listar usuarios(GET)
- Criar usuarios(POST)
- Modificar usuario(PUT)
- Remover usuario(DELETE)

# Criando rotas para a aplicação
- criando pasta routes/userRoutes.js
- importando arquivo com require para o index

# Caminho para arquivo json, para simular a interação com banco de dados
- const filePath = join(__dirname, 'users.json')

# Verificando se a rota existe, e retornando seus valores em JSON
"(
    //Verificando se o arquivo existe no caminho acima, se não existir, data valera um array vazio
const getUsers = () => {
  const data = fs.existsSync(filePath) ? fs.readFileSync(filePath) : [];

    try {
        return JSON.parse(data)
    } catch (error) {
        return []
    }
}
)"

# Salvando os dados dos usuarios do arquivo users.json
"(
    const saveUsers = (users) => fs.writeFileSync(filePath, JSON.stringify(users, null, '\t'))
    )"

# Exportando useRoutes para o index, e usando app(express) como dependencia

# Utilizando o Postman para simular requisições HTTP
- Requisição do tipo GET  http://localhost:3000/users

# Criando metodo para criar um usuario
- "(

    const userRoute = (app) => {
    app.route('/users/:id?').get((req, res) => {
        const users = getUsers()
        res.send({ users })
    })
        //Metodo para criação de usuario
        .post((req, res) => {
            const users = getUsers()

            users.push(req.body)
            saveUsers(users)

            res.status(201).send('OK')
        })
}

)"


# Instalando Middleware
- transformando dados que vem do cabecario HTTP da req.body em um objeto utilizando o metodo body.parse
- "(
    const bodyParse = require('body-parser')

    app.use(bodyParser.urlencoded({ extended: false }))
)"

# Requisicao POST feita pelo Postman

# Utilizando metodo PUT para atualizacao do usuario
- "(

    const userRoute = (app) => {
    app.route('/users/:id?').get((req, res) => {
        const users = getUsers()
        res.send({ users })
    })
        //Metodo para criação de usuario
        .post((req, res) => {
            const users = getUsers()

            users.push(req.body)
            saveUsers(users)

            res.status(201).send('OK')
        })
        //Metodo para atualização de usuarios
        .put((req, res) => {
            const users = getUsers()

            saveUsers(users.map(user => {
                if (user.id === req.params.id) {
                    return {
                        ...user,
                        ...req.body
                    }
                }
                return user
            }))

            res.status(200).send('OK')
        })
}

)"

# Utilizando metodo PUT no postman para att o usuario

# Adicionando metodo delete
- "(

    .delete((req, res) => {
            const users = getUsers()

            saveUsers(users.filter(user => {
                return user.id !== req.params.id
            }))
            res.status(200).send('OK')
        })
)"