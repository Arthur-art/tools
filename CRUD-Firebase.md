# Instructions CRUD

# Iniciando o projeto

- yarn create next-app nome-do-projeto
- yarn dev

# Instalando chakra para parte do front-end

- https://chakra-ui.com/docs/getting-started
- yarn add @chakra-ui/react @emotion/react@^11 @emotion/styled@^11 framer-motion@^4
- atualize a page _app.js 
```js

    import { ChakraProvider } from "@chakra-ui/react"

      function MyApp({ Component, pageProps }) {
          return <ChakraProvider>
                    <Component {...pageProps} />
                 </ChakraProvider>
}

export default MyApp
```

# Montando o forms no index do projeto usando chakra documentation

# Instalando o formik e o yup para validação de formulario
- formik sera usado para gerenciar o formulario, o states do formulario

- yarn add formik yup

# Configurando o formik 
- adicionando codigo na page onde formik sera usado para manipular os valores
- import { useFormik } from "formik";
```js
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
 ```
- Adicionando handleChange, values.email, values.password, values.endereco nos inputs dos campos que serão salvos no formik
- Exemplo :
```js


            <FormControl isRequired id="email">
            <FormLabel>E-mail</FormLabel>
            <Input type="email" value={values.email} onChange={handleChange} onBlur={handleBlur} />
             {touched.email && (<FormHelperText textColor="#e74c3c"> {errors.email}</FormHelperText>)}
            </FormControl>

```

# Acessando o git da doc do yup
- https://github.com/jquense/yup
- adicionando o bloco de  codigo do schema:
```js
    const validationSchema = yup.object().shape({
  email: yup
    .string()
    .email("E-mail inválido")
    .required("Preenchimento obrigatório!"),
  password: yup.string().required("Preenchimento obrigatório!"),
  username: yup.string().required("Preenchimento obrigatório!"),
});
```

# Instalando o firebase (Para o banco de dados e para autenticacao)

- yarn add firebase firebase-admin
- registre o app no firebase

# Criando um novo projeto no firebase para cadastrar usuario e conseguir fazer login
- https://console.firebase.google.com/u/0/?hl=pt&pli=1

- crie um novo projeto no console

- depois de criado adicione um app para começar (Web no caso)

# Configurando o firebase
- Adicionando pastas "config/firebase/" com um arquivo "index.js" na pasta raiz do projeto
- Adicionando codigo ao arquivo index da pasta firebase - 
```js
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
```
- Para evitar que o firebase seja recriado pelo hot reload do next 
  - const app = firebaseClient.apps.length ? firebaseClient.app() : firebaseClient.initializeApp(firebaseConfig);


- import firebase para o index do projeto, e para os components 

- dentro do onSubmit do Formik para cadastrar um usuario no firebase,  adicione
```js

onSubmit: async (values, form) => {
                try {
                    const user = await app.auth().createUserWithEmailAndPassword(values.email, values.password)
                    console.log(user)

                } catch (error) {
                    console.log('erro', error)
                }
            },
```


# Configurando formik na page Login

- adicione o codigo na page do login 
```js


    const [newValue, setNewValue] = useState("")

    const [newValuePass, setNewValuePass] = useState("")

    const [values, setValues] = useState({})

    const handleChangeNewValueEmail = ({ target }) => {
        const { value } = target;
        setNewValue(value)
    }
    const handleChangeNewValuePass = ({ target }) => {
        const { value } = target;
        setNewValuePass(value)
    }

    const check = () => {
        try {
            const user = app.auth().signInWithEmailAndPassword(values.email, values.password)

        } catch (error) {
            console.log('erro', error)
        }
    }

    const handleSubmit = () => {
        setValues({
            email: String(newValue),
            password: String(newValuePass)
        })
    }

    useEffect(() => {
        check()
    }, [values.email, values.password])


```

  
# Verificacao de persistencia do Auth no arquivo client do firebase

- https://firebase.google.com/docs/auth/web/auth-state-persistence?authuser=0

- Adicionando codigo na config do firebase
  
```js
  
   export const persistenceMode = firebaseClient.auth.Auth.Persistence.LOCAL
```

- importando persistenceMode no index do Login

- Adicionando na index do login 
```js

       useEffect(() => {
        check()
        app.auth().onAuthStateChanged(user => {
            setloginSucess({
                loading: false,
                user
            })
        })
    }, [values.email, values.password])

```

# Criando arquivo .env na raiz do projeto para guardar variaveis de ambiente
- alterando no arquivo client do firebase 
```js
  const firebaseConfig = {
    apiKey: process.env.NEXT_PUBLIC_API_KEY,
    authDomain: process.env.NEXT_PUBLIC_AUTH_DOMAIN,
    projectId: process.env.NEXT_PUBLIC_PROJECT_ID,
    storageBucket: process.env.NEXT_PUBLIC_STORAGE_BUCKET,
    messagingSenderId: process.env.NEXT_PUBLIC_MESSAGING_SENDER_ID,
    appId: process.env.NEXT_PUBLIC_APP_ID,
    measurementId: process.env.NEXT_PUBLIC_MEASUREMENT_ID,
};

```

- Guardando os dados das variaveis acima no arquivo .env 


# Deploy in vercel
- vercel
- vercel --prod
