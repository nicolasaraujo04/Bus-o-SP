# 🚌 Busão SP

**Aplicativo mobile para quem anda de ônibus em São Paulo**: encontra os pontos mais próximos pelo GPS,
mostra quando o ônibus chega, avisa a hora certa de sair de casa e vibra quando o passageiro está chegando
ao ponto de descida. Tudo com dados em tempo real da **API Olho Vivo da SPTrans**.

| | |
|---|---|
| **Disciplina** | Cross-Platform Application Development — 2º ano de Ciência da Computação |
| **Professor** | Rodrigo Moraes |
| **Trabalho** | Global Solution 2026.2 — Mobilidade em São Paulo com a API Olho Vivo |
| **Área de desafio** | Citizen Intelligence & Predictive Analytics |
| **Tecnologias** | React Native · Expo · TypeScript · Expo Router |
| **Status** | ✅ Concluído |

## 🎥 Vídeo de demonstração

▶️ **[Assistir no YouTube](COLE_AQUI_O_LINK_DO_VIDEO)**

O vídeo mostra o app rodando num celular real com dados da SPTrans: o pedido de permissão e o uso do
GPS, os pontos próximos, a previsão de chegada, a criação de um alerta, a **notificação com vibração**
e o mapa da linha com os ônibus em tempo real.

## 💡 O problema

Quem depende de ônibus em São Paulo:

- fica parado no ponto sem saber quando o ônibus vai passar;
- sai de casa cedo demais (e espera muito) ou tarde demais (e perde o ônibus);
- em trajetos desconhecidos, à noite ou com baixa visão, tem medo de passar do ponto de descida;
- precisa saber se o próximo veículo é acessível.

## ✅ A solução

O Busão SP junta o **GPS do celular** com as **previsões em tempo real da SPTrans** e avisa o usuário
na hora certa, por **notificação, vibração e alerta na tela**.

## 📱 Funcionalidades

| Funcionalidade | O que faz | Recursos usados |
|---|---|---|
| **📍 Perto de mim** | Lista os pontos de ônibus a até 800 m do usuário, com distância e tempo a pé. Também permite buscar por rua, com os resultados ordenados pela distância | GPS + `/Parada/Buscar` |
| **⏱️ Próximos ônibus** | Painel do ponto com a previsão de todas as linhas, atualizado a cada 30 s, e filtro "só veículos acessíveis ♿" | `/Previsao/Parada` |
| **🚶 Hora de sair** | Compara a caminhada até o ponto com a chegada do ônibus e mostra "Saia em 3 min", "Saia agora!" ou "Não dá tempo a pé" | GPS + previsão |
| **🔔 Meu ônibus está chegando** | O usuário escolhe a linha e quando quer ser avisado (3, 5 ou 10 min antes, ou "na hora de sair") e recebe notificação + vibração | `/Previsao` + notificações |
| **🔎 Busca de linhas** | Encontra linhas pelo número ou destino (ex.: "8000", "Lapa"), separadas por sentido | `/Linha/Buscar` |
| **🗺️ Mapa da linha** | Mostra o trajeto, os pontos, os ônibus em tempo real (atualizados a cada 20 s) e a posição do usuário | `/Posicao/Linha` + `/Parada/BuscarParadasPorLinha` + GPS |
| **🛑 Desça no ponto certo** | Acompanha o usuário durante a viagem: um aviso prévio a ~750 m e o alerta final a 300 m do ponto de descida, com vibração forte | GPS contínuo + notificações + vibração |
| **📋 Central de alertas** | Mostra os alertas ativos e o status da permissão de notificações, com um botão para testar a notificação e a vibração | Notificações + vibração |

## 🛰️ Uso do GPS e dos alertas

### Pontos "perto de mim"

A API Olho Vivo não tem busca por coordenada, só por nome ou endereço. Por isso criamos este algoritmo:

1. lê a posição do usuário e de 4 pontos a 250 m dela (norte, sul, leste e oeste);
2. descobre o nome dessas ruas com o geocodificador do celular (`reverseGeocodeAsync`);
3. busca os pontos dessas ruas em `/Parada/Buscar`, todas as buscas em paralelo;
4. remove os pontos repetidos, calcula a distância de cada um (fórmula de Haversine) e mostra os mais próximos.

### Alertas

- **Alerta de chegada:** o app consulta a previsão da linha no ponto a cada 30 s. Quando o ônibus está
  a poucos minutos, dispara uma notificação local, vibra e mostra um alerta na tela. Se o app for para
  segundo plano, fica agendada uma notificação baseada na última previsão, para o usuário não perder o aviso.
- **Alerta de descida:** o GPS acompanha o usuário (a cada 15 m ou 5 s) e o app mantém a tela ligada
  durante a viagem.

### Permissões bem tratadas

- O GPS é pedido na primeira abertura, com uma explicação clara do motivo.
- A permissão de notificações só é pedida quando o usuário cria o primeiro alerta.
- Há uma tela própria para cada situação: permissão negada (botão "Permitir"), negada de vez
  (botão "Abrir configurações"), GPS desligado e erro de leitura.
- Sem permissão de notificação, o app continua avisando com vibração e alerta na tela.

## ♿ Usabilidade e acessibilidade

- Fontes grandes, alto contraste e botões com área de toque de no mínimo 48 pt.
- Rótulos de acessibilidade para leitores de tela (TalkBack / VoiceOver), por exemplo:
  "Ônibus em 4 minutos, acessível".
- Destaque e filtro para veículos acessíveis.
- Todas as telas têm estados de carregamento, lista vazia e erro, com o botão "Tentar de novo".
- Navegação simples por 4 abas: **Perto de mim**, **Linhas**, **Alertas** e **Sobre**.

## 🔌 Endpoints da API Olho Vivo utilizados

Base: `https://api.olhovivo.sptrans.com.br/v2.1`

| Endpoint | Uso no app |
|---|---|
| `POST /Login/Autenticar?token=` | Autenticação. O app refaz o login sozinho se a sessão expirar (erro 401) |
| `GET /Linha/Buscar?termosBusca=` | Busca de linhas |
| `GET /Parada/Buscar?termosBusca=` | Pontos próximos e busca por rua |
| `GET /Parada/BuscarParadasPorLinha?codigoLinha=` | Pontos e trajeto da linha no mapa |
| `GET /Posicao/Linha?codigoLinha=` | Ônibus em tempo real no mapa |
| `GET /Previsao/Parada?codigoParada=` | Painel "próximos ônibus" |
| `GET /Previsao?codigoParada=&codigoLinha=` | Monitoramento do alerta de chegada |

## 🚀 Como executar

### Pré-requisitos

- Node.js 20 ou mais recente
- Aplicativo **Expo Go** no celular ([Play Store](https://play.google.com/store/apps/details?id=host.exp.exponent) / [App Store](https://apps.apple.com/app/expo-go/id982107779))
- Token da API Olho Vivo, gerado em <https://www.sptrans.com.br/desenvolvedores/> → **Meus Aplicativos**

### Passo a passo

```bash
# 1. Clonar o repositório
git clone https://github.com/nicolasaraujo04/Bus-o-SP
cd busao-sp

# 2. Configurar o token da SPTrans
cp .env.example .env
# abra o .env e preencha: EXPO_PUBLIC_OLHOVIVO_TOKEN=seu_token

# 3. Instalar as dependências
npm install
npm run setup        # instala as bibliotecas nas versões compatíveis com o SDK do Expo

# 4. Iniciar
npx expo start
```

5. Leia o QR Code com o **Expo Go** (Android) ou com a **câmera** (iPhone).
6. O celular e o computador precisam estar na mesma rede Wi-Fi. Se não for possível, use `npx expo start --tunnel`.

> O arquivo `.env` está no `.gitignore`: o token não vai para o GitHub.
> Depois de alterar o `.env`, reinicie com `npx expo start --clear`.

## 🗂️ Estrutura do projeto

```
busao-sp/
├── app/                              # Telas (Expo Router: cada arquivo é uma rota)
│   ├── _layout.tsx                   # Navegação em pilha + provider dos alertas
│   ├── (tabs)/
│   │   ├── _layout.tsx               # Abas inferiores
│   │   ├── index.tsx                 # Perto de mim
│   │   ├── linhas.tsx                # Busca de linhas
│   │   ├── alertas.tsx               # Central de alertas
│   │   └── sobre.tsx                 # Sobre e créditos da SPTrans
│   ├── parada/[codigo].tsx           # Próximos ônibus + Hora de sair + criar alerta
│   └── linha/[codigo].tsx            # Mapa da linha + ponto de descida
├── src/
│   ├── api/
│   │   ├── olhoVivo.ts               # Cliente da API (login, sessão, nova tentativa)
│   │   └── tipos.ts                  # Tipos TypeScript das respostas
│   ├── contexto/MonitorContext.tsx   # Monitor global dos alertas
│   ├── hooks/useLocalizacao.ts       # Permissão + posição do GPS
│   ├── servicos/
│   │   ├── localizacao.ts            # GPS (permissão, posição com tempo limite)
│   │   ├── notificacoes.ts           # Notificações locais, canal Android, vibração
│   │   └── paradasProximas.ts        # Algoritmo de pontos próximos
│   ├── componentes/                  # Botão, cartões, mapa, modal, mensagens
│   ├── utils/                        # Distância, tempo a pé, minutos até o ônibus
│   └── tema.ts                       # Cores e tamanhos
├── docs/
│   ├── GUIA_DO_CODIGO.md             # Explicação detalhada do código
│   └── ROTEIRO_VIDEO.md              # Roteiro do vídeo de demonstração
├── INTEGRANTES.txt                   # Integrantes e divisão do trabalho
└── .env.example                      # Modelo do arquivo de configuração
```

### Organização do código

- **Separação de responsabilidades:** a comunicação com a API (`api/`), a lógica de negócio
  (`servicos/`, `contexto/`), os cálculos puros (`utils/`) e a interface (`app/`, `componentes/`)
  ficam em pastas separadas.
- **TypeScript em modo estrito:** todas as respostas da API são tipadas.
- **Componentes reutilizáveis:** `Botao`, `Mensagem`, `CartaoParada`, `PermissaoGPS` e outros.
- **Estado global com Context API:** os alertas continuam ativos ao trocar de tela.
- **Limpeza de efeitos:** todo `setInterval` e todo acompanhamento de GPS é encerrado quando a tela
  ou o alerta termina, o que economiza bateria e evita vazamentos de memória.

## 📦 Bibliotecas

| Biblioteca | Para quê |
|---|---|
| `expo-location` | GPS: posição atual, acompanhamento contínuo e geocodificação reversa |
| `expo-notifications` | Notificações locais (funcionam no Expo Go) |
| `Alert` e `Vibration` (React Native) | Alertas e vibração imediatos |
| `react-native-maps` | Mapa da linha |
| `expo-keep-awake` | Mantém a tela ligada durante a viagem |
| `expo-router` | Navegação por abas e pilha |
| `@expo/vector-icons` | Ícones |

## 📝 Observações

- O Expo Go não permite GPS em segundo plano. Por isso, no modo "Desça no ponto certo", o app
  mantém a tela ligada e deve ficar aberto durante a viagem.
- Com o app em segundo plano, o aviso de chegada usa a última previsão recebida, que pode variar com o trânsito.

## 👥 Equipe

| Nome | RM | Principais contribuições |
|---|---|---|
| NOME DO INTEGRANTE 1 | RM00000 | Cliente da API Olho Vivo, busca de linhas e mapa da linha |
| NOME DO INTEGRANTE 2 | RM00000 | GPS, algoritmo de pontos próximos, telas "Perto de mim" e "Próximos ônibus" |
| NOME DO INTEGRANTE 3 | RM00000 | Notificações, vibração, monitor de alertas, tela de alertas e vídeo |

A divisão detalhada está em [`INTEGRANTES.txt`](INTEGRANTES.txt).

## 📄 Créditos

Dados de linhas, pontos, posição dos veículos e previsões de chegada fornecidos pela
**SPTrans (São Paulo Transporte S/A) por meio da API Olho Vivo**.
Os créditos também aparecem dentro do app, em todas as telas com dados e na aba **Sobre**.
