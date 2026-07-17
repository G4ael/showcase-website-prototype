# Como ativar o catálogo online (Firebase) — passo a passo

Depois desta configuração (feita **uma única vez**, uns 20 minutos), você vai
adicionar, editar e excluir produtos **pelo próprio site**, na "Área do lojista"
(link no rodapé), sem nunca mais mexer em código.

Tudo aqui usa o **plano gratuito** do Firebase (serviço do Google). Não precisa
cadastrar cartão de crédito.

---

## Passo 1 — Criar o projeto

1. Acesse **https://console.firebase.google.com** e entre com sua conta Google.
2. Clique em **"Criar um projeto"** (ou "Adicionar projeto").
3. Nome do projeto: por exemplo `mostruario-marcenaria`. Avance.
4. Quando perguntar sobre o **Google Analytics**, pode **desativar** — não é necessário.
5. Aguarde criar e clique em **Continuar**.

## Passo 2 — Criar o login do lojista

1. No menu lateral, abra **Criação (Build) → Authentication** e clique em **Vamos começar**.
2. Na aba **Método de login**, escolha **E-mail/senha** e **ative** (só a primeira chave). Salve.
3. Na aba **Usuários (Users)**, clique em **Adicionar usuário**:
   - E-mail: o seu (ex.: `djoogabriel2@gmail.com`)
   - Senha: crie uma senha forte — **este será o login da Área do lojista**.

## Passo 3 — Criar o banco de dados dos produtos

1. No menu lateral, abra **Criação (Build) → Firestore Database** e clique em **Criar banco de dados**.
2. Local: escolha **southamerica-east1 (São Paulo)**. Avance.
3. Modo: escolha **modo de produção**. Criar.
4. Abra a aba **Regras (Rules)**, apague o que estiver lá e cole exatamente isto:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /produtos/{produto} {
      allow read: if true;                    // qualquer visitante vê os produtos
      allow write: if request.auth != null;   // só quem fez login pode alterar
    }
  }
}
```

5. Clique em **Publicar**.

> Essas regras são a segurança do site: todo mundo pode **ver** o catálogo,
> mas só quem entrar com o e-mail e senha do Passo 2 pode **alterar**.

## Passo 4 — Conectar o site ao projeto

1. Clique na **engrenagem ⚙ (Configurações do projeto)**, no topo do menu lateral.
2. Role até **"Seus aplicativos"** e clique no ícone **`</>` (Web)**.
3. Apelido do app: `site` (qualquer nome serve). **Não** marque Firebase Hosting. Registrar.
4. Vai aparecer um trecho de código com `const firebaseConfig = { ... }`.
   Copie **só os valores** entre chaves.
5. Abra o arquivo **index.html** num editor de texto (Bloco de Notas serve),
   procure por `FIREBASE_CONFIG` (bem no comecinho do `<script>`, marcado com ★★★)
   e substitua cada `"COLE_AQUI"` pelo valor correspondente. Exemplo:

```js
const FIREBASE_CONFIG = {
  apiKey:            "AIzaSyC...suachave...",
  authDomain:        "mostruario-marcenaria.firebaseapp.com",
  projectId:         "mostruario-marcenaria",
  storageBucket:     "mostruario-marcenaria.appspot.com",
  messagingSenderId: "123456789012",
  appId:             "1:123456789012:web:abc123def456"
};
```

> Pode deixar essa configuração visível no site sem medo: ela é pública por
> natureza (identifica o projeto, não dá acesso). Quem protege os dados são
> as regras do Passo 3 e a sua senha.

Essa é a **única** edição de código de todo o processo.

## Passo 5 — Publicar o site e autorizar o endereço

1. Publique o `index.html` em qualquer hospedagem de site estático
   (Vercel, Netlify Drop, GitHub Pages, ou a hospedagem que preferir).
2. No Firebase, vá em **Authentication → Configurações (Settings) →
   Domínios autorizados** e clique em **Adicionar domínio**, informando o
   endereço do seu site (ex.: `mostruario-marcenaria.vercel.app`).
   - `localhost` já vem autorizado, então testes no seu computador funcionam direto.

## Passo 6 — Usar no dia a dia 🎉

1. Abra o site e clique em **"Área do lojista"** no rodapé
   (ou acrescente `#admin` no fim do endereço).
2. Entre com o e-mail e a senha do Passo 2.
3. Pronto:
   - **+ Adicionar produto** — na barra que aparece embaixo;
   - **✏️ Editar** / **🗑️ Excluir** — nos botões sobre cada card;
   - Cada produto aceita **até 5 fotos**, com legenda opcional e setinhas
     ◀ ▶ para mudar a ordem (a 1ª foto é a capa do card);
   - As fotos são comprimidas automaticamente no navegador — pode mandar
     foto de celular sem medo.
4. Qualquer alteração aparece **na hora para todos os visitantes**. Sem código,
   sem git, sem publicar de novo.

Os produtos de exemplo (desenhos de móveis) somem sozinhos assim que você
cadastrar o primeiro produto de verdade — e voltam se o catálogo ficar vazio.

---

## Limites do plano gratuito (tranquilos para um mostruário)

| Recurso | Limite grátis | O que significa na prática |
|---|---|---|
| Armazenamento | 1 GB | ~1.500 fotos comprimidas |
| Leituras | 50.000/dia | Milhares de visitas por dia |
| Gravações | 20.000/dia | Você editando o catálogo à vontade |

Se um dia o site crescer muito, o Firebase avisa antes de qualquer cobrança.
