---
title: Um aprendizado de Performance
separator: <!-- p -->
verticalSeparator: <!-- v -->
theme: night
revealOptions:
  transition: 'concave'
---

## Um aprendizado de workflow de performance

> tava bela mas faltava performance na tabela de Performance

<!-- v -->

![A Tabela](/assets/tabela.gif)

<!-- p -->

### O que geralmente pensamos?

- Tira o Material UI 👹
- Convence o cliente que a tabela não é uma boa UX 😅
- 🔥🔥🔥 Refactoring 🔥🔥🔥

<!-- v -->

### Mas...

- Precisamos de testes confiáveis para um bom refactoring e testes que testam a implementação não são confiáveis
- Tirar o Material UI poderia ser um grande refactoring
- O cliente AMA a tabela do jeitinho que é

<!-- p -->

### Plano de ação:

- Menos calls para `useCroods`
- Usar `stateID`
- `React.memo` ao redor das linhas
- `useMemo` e `useCallback` para _expensive calculations_
- Queue para chamar o backend
- [Lifting the expensive component](https://codesandbox.io/s/react-codesandbox-qtdob?from-embed)
- Colocar chamada em outra thread: [webpack workerize](https://github.com/developit/workerize-loader)

<!-- p -->

### Menos calls para `useCroods`

- Lembrar que o `useCroods` é uma função e registra listeners para cada chamada
- Todos os `useXXX` são hooks e hooks são - ou deveriam ser - efeitos colaterais
- Se você tem dezenas de linhas com efeitos colaterais fica difícil de controlar a quantidade de rerenders
- Lift the Croods! Componentes pequenos mais puros

<!-- v -->

![Lift Croods](/assets/lift-croods-1.png)

<!-- v -->

![Lift Croods 2](/assets/lift-croods-2.png)

<!-- p -->

#### Usar `stateID`

- Alocaria mais memória e provavelmente menos processamento
- Não foi preciso uma vez que levamos o Croods para o Parent
<!-- p -->

#### `React.memo`

- Foi usado nas linhas da tabela
- Agora que esses componentes eram puros, somente a mudança nos props iria provocar rerenders

![React.memo](/assets/react-memo.png)


<!-- p -->

#### `useMemo` e `useCallback`

- Raramente úteis e provavelmente não contribuiram muito aqui
- Encontrar funções que tivessem execuções _expensive_ e usar essas otimizações

![useCallback](/assets/usecallback.png)


<!-- p -->

#### Queue

```js
import { useRef } from 'react'
import { useInterval } from '@seasonedsoftware/utils'

function useQueue(fn, timeout = 300) {
  const queue = useRef([])

  const addToQueue = (params) => {
    queue.current.push(params)
  }

  useInterval(async () => {
    await Promise.allSettled(queue.current.map(fn))
    queue.current = []
  }, timeout)

  return addToQueue
}

export { useQueue }
```

<!-- v -->
![Resultado queue](/assets/queue.gif)

<!-- p -->

### Mais uma bateria de analizes
![Analize](/assets/analize.png)

<!-- v -->
![Analize](/assets/analize-mui-1.png)
<!-- v -->
![Analize](/assets/analize-mui-1-2.png)
<!-- v -->
![Analize](/assets/analize-mui-2.png)
<!-- v -->
![Analize](/assets/analize-mui-2-2.png)

<!-- p -->
### E se tirarmos o Select do MUI?
![MUI](/assets/mui.png)


<!-- p -->

## Resultado
![Resultado](/assets/resultado.gif)

<!-- p -->

![RIP MUI](/assets/bye-material.jpg)
