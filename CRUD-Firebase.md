# Instructions CRUD

# Iniciando o projeto

- yarn create next-app nome-do-projeto
- yarn dev

# Instalando chakra para parte do front-end

- https://chakra-ui.com/docs/getting-started
- yarn add @chakra-ui/react @emotion/react@^11 @emotion/styled@^11 framer-motion@^4
- atualize a page _app.js"(

    import { ChakraProvider } from "@chakra-ui/react"

function MyApp({ Component, pageProps }) {
  return <ChakraProvider>
    <Component {...pageProps} />
  </ChakraProvider>
}

export default MyApp

)"

# Montando o forms no index do projeto usando chakra documentation

# Instalando o formik e o yup para validação de formulario
- formik sera usado para gerenciar o formulario, o states do formulario

- yarn add formik yup

# Configurando o formik 
- adicionando codigo na page onde formik sera usado para manipular os valores
(
  const {
        values,
        errors,
        touched,
        handleChange,
        handleBlur,
        handleSubmit,
        isSubmitting } = useFormik({

            onSubmit: (values, form) => {
                console.log(values)
            },
            validationSchema,
            initialValues: {
                email: '',
                username: '',
                password: '',
                endereco: ''
            },

        })
)
- Adicionando handleChange, values.email, values.password, values.endereco nos inputs dos campos que serão salvos no formik

# Acessando o git da doc do yup
- https://github.com/jquense/yup
- adicionando o bloco de  codigo do schema"(
    
    const validationSchema = yup.object().shape({
  email: yup
    .string()
    .email("E-mail inválido")
    .required("Preenchimento obrigatório!"),
  password: yup.string().required("Preenchimento obrigatório!"),
  username: yup.string().required("Preenchimento obrigatório!"),
});

)"

- adicionando o mesmo no index-Login e signup do projeto para validar os campos do forms

- criando pastas Agenda, Login e adicionando components jsx de Agenda e Login 
- exportando esses components para serem reenderizados no index do projeto 


# Criando um novo projeto no firebase para cadastrar usuario e conseguir fazer login
- https://console.firebase.google.com/u/0/?hl=pt&pli=1

# Instalando o firebase (Para o banco de dados e para autenticacao)

- yarn add firebase firebase-admin
- registre o app no firebase

# Configurando o firebase
- Adicionando pastas "config/firebase/" com um arquivo "client.js" na pasta firebase na raiz do projeto
- Adicionando codigo ao arquivo client da pasta firebase - "(

import firebaseClient from 'firebase/app'
import 'firebase/auth'
const firebaseConfig = {
    apiKey: process.env.NEXT_PUBLIC_API_KEY,
    authDomain: process.env.NEXT_PUBLIC_AUTH_DOMAIN,
    projectId: process.env.NEXT_PUBLIC_PROJECT_ID,
    storageBucket: process.env.NEXT_PUBLIC_STORAGE_BUCKET,
    messagingSenderId: process.env.NEXT_PUBLIC_MESSAGING_SENDER_ID,
    appId: process.env.NEXT_PUBLIC_APP_ID,
    measurementId: process.env.NEXT_PUBLIC_MEASUREMENT_ID,
};

//Para evitar que o firebase seja recriado pelo hot reload do next 
const app = firebaseClient.apps.length ? firebaseClient.app() : firebaseClient.initializeApp(firebaseConfig);

//Verificacao de persistencia do Auth
export const persistenceMode = firebaseClient.auth.Auth.Persistence.LOCAL;


export { firebaseClient }

- import firebase para o index do projeto, e para os components 
- autenticacao feita direto do site do firebase('facebook','email')


# Criando arquivo .env na raiz do projeto para guardar variaveis de ambiente
- alterando no arquivo client do firebase "(

  const firebaseConfig = {
    apiKey: process.env.NEXT_PUBLIC_API_KEY,
    authDomain: process.env.NEXT_PUBLIC_AUTH_DOMAIN,
    projectId: process.env.NEXT_PUBLIC_PROJECT_ID,
    storageBucket: process.env.NEXT_PUBLIC_STORAGE_BUCKET,
    messagingSenderId: process.env.NEXT_PUBLIC_MESSAGING_SENDER_ID,
    appId: process.env.NEXT_PUBLIC_APP_ID,
    measurementId: process.env.NEXT_PUBLIC_MEASUREMENT_ID,
};

)"

- Guardando os dados das variaveis acima no arquivo .env 

# Verificacao de persistencia do Auth no arquivo client do firebase

- https://firebase.google.com/docs/auth/web/auth-state-persistence?authuser=0
- export const persistenceMode = firebaseClient.auth.Auth.Persistence.LOCAL;

# Deploy in vercel

# Firebase Cloud Firestore - Criando banco de dados
- acesse firestore no site do firebase
- crie um banco de dados
- em configuracoes do projeto  /  contas de serviço - crie uma conta de serviço
- em config/firebase crie um arquivo server.js - adicione "(

import admin from 'firebase-admin'

const app = firebase.apps.length ? firebase.app() : firebase.initializeApp({
    credential: admin.credential.cert({
        type: process.env.TYPE,
    })
});

export default admin;


)"

- gere uma nova chave privada pelo firestore

# Profile do usuario feito via API rest
- Utilizando axios
- yarn add axios


# Gerenciando usuarios 
- Guardando o id do usuario no back da aplicacao
- https://firebase.google.com/docs/auth/web/manage-users?authuser=0


# Criando component Auth/index.js (Provider) para gerenciar tudo que for autenticacao
- refatorando signup, Login/index, pages/index passando a config de auth para o component Auth