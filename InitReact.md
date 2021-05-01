# Iniciando o projeto com react

- yarn create react-app (nome-app) --template=typescript

- yarn start

# Iniciando o projeto com next

- yarn create next-app (nome-app)
- yarn dev

# Instalando chakra para parte do front-end

- https://chakra-ui.com/docs/getting-started
- yarn add @chakra-ui/react @emotion/react@^11 @emotion/styled@^11 framer-motion@^4
- se for next-app faça: 

Atualize a page _app.js"(

import { ChakraProvider } from "@chakra-ui/react"

function MyApp({ Component, pageProps }) {
  return <ChakraProvider>
    <Component {...pageProps} />
  </ChakraProvider>
}

export default MyApp

)"


# Deploy

- Conta no vercel

- npm i -g vercel

- vercel login

- vercel

Vercel CLI 21.3.1
? Set up and deploy “~\OneDrive\Documentos\Repository\NLW4-React\moveit-next”? [Y/n] y
? Which scope do you want to deploy to? arthur-art
? Link to existing project? [y/N] n
? What’s your project’s name? moveiarthurart
? In which directory is your code located? ./
Auto-detected Project Settings (Next.js):
- Build Command: `npm run build` or `next build`
- Output Directory: Next.js default
- Development Command: next dev --port $PORT
? Want to override the settings? [y/N] n
🔗  Linked to arthur-art/moveiarthurart (created .vercel and added it to .gitignore)
🔍  Inspect: https://vercel.com/arthur-art/moveiarthurart/CpJA5Tfq3TVfWDYBoG4GEoTeEPM8 [1s]
✅  Production: https://moveiarthurart.vercel.app [copied to clipboard] [42s]
📝  Deployed to production. Run `vercel --prod` to overwrite later (https://vercel.link/2F).
💡  To change the domain or build command, go to https://vercel.com/arthur-art/moveiarthurart/settings

-- vercel --prod