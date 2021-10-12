# Introduzindo Error Boundaries
- Um erro de JavaScript em uma parte da UI não deve quebrar toda a aplicação. Para resolver este problema para usuários do React, o React 16 introduziu um novo conceito de “error boundary”.
- Error boundaries são componentes React que capturam erros de JavaScript em qualquer lugar na sua árvore de componentes filhos, registram esses erros e mostram uma UI alternativa em vez da árvore de componentes que quebrou

# Formas de usar o Error Boundary
- Na prática, na maioria das vezes você irá declarar um componente error boundary uma vez e usá-lo em toda a aplicação.
- Após criar um component de classe como um component que recebe um Childreen ou instalar uma lib para o uso do mesmo, você utiliza esse component afim de envolver outro component que você precise que seja capturado um erro
- Ex.:
```js
<ErrorBoundary>
  <MyWidget />
</ErrorBoundary>
```

# Onde colocar error boundaries
- Você é quem decide a granularidade das errors boundaries. Você pode envolver componentes da rota superior para exibir uma mensagem como “Algo deu errado” para o usuário, da mesma forma que frameworks server-side costumam lidar com travamentos. Você também pode envolver widgets individuais em uma error boundary para protegê-los de quebrar o restante da aplicação.

# Exemplo do uso de um error boundary
- Neste projeto eu implementei o error boundary afim de previnir que a página quebre ao intecionalmente tentar iterar em um array que no final retorna um null em vez de um objeto;
- Ao capturar o erro o component ErrorBoundary reenderiza um component a minha escolha, no lugar do component que poderia quebrar a página

- Exemplo com component jsx:
```json
https://github.com/Arthur-art/Reactjs-insights/blob/master/src/components/ErrorBoundary/index.jsx
```
- Exemplo com component tsx:
```json
https://github.com/Arthur-art/ReactJs-TypeScript/blob/master/src/App.tsx
```

# Links para a documentação oficial de uma implementação do ErrorBoundary com Typescript:
- https://github.com/bvaughn/react-error-boundary#installation
