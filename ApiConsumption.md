# Maneiras de se consumir uma Api:

# Modelo com fetch e SSR(server-side rendering):
```js
 useEffect(() => {
    fetch('http://localhost:3333/episodes').then((response) => {
      return response.json()
    })
      .then((data) => {
        console.log(data)
      })
  }, [])


# Modelo SSR(server side redering), requisição feita na camada do next

- Dentro de qualquer arquivo na pasta pages export uma function chamada (getServerSideProps)
     
    export async function getServerSideProps(){
        const response = await fetch('http://localhost:3333/episodes')
        const data = await response.json()

        return{
            props:{
                episode: data
            }
        }
}

```

# Modelo SSG(static site generators), requisição feita na camada do next

- Dentro de qualquer arquivo na pasta pages export uma function chamada (getStaticProps)

```js
     
    export async function getStaticProps(){
        const response = await fetch('http://localhost:3333/episodes')
        const data = await response.json()

        return{
            props:{
                episode: data
            },

            //a cada 8 horas ira gerar uma nova versão desta pagina
            revalidate: 60 * 60 *8,

        }
}

```

# Adicionando axios
- yarn add axios
- criando no src uma pasta services/api.ts(

```js
  import axios from 'axios'

export const api = axios.create({
    baseURL: 'http://localhost:3333/'
})

)
- Atualizando requisição SSG (

  export const getStaticProps: GetStaticProps = async () => {
  const { data } = await api.get('episodes', {
    params: {
      _limit: 12,
      _sort: 'published_at',
      _order: 'desc'
    }
  });

  return {
    props: {
      episodes: data
    },

    revalidate: 60 * 60 * 8,
  }
  ```
)
