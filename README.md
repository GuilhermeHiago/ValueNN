# ValueNN
Exercise for understanding how ValueNetworks models are trained using a simplified version of Amazons

## Problema Alvo
Criar um modelo capaz de jogar de jogar Amazons (um jogo de tabuleiro). A proposta de 
solução se baseia em fazer com que um modelo de redes neurais aprenda quais jogadas 
(estados de jogo) são melhores, para usá-lo como função heurística futuramente em uma 
arvore de busca.

## Dataset
Como não havia um conjunto de dados disponível, precisamos gerá-lo, simulando jogos 
completos com jogadas aleatórias. Ao final de cada jogo, caso o jogador 1 ganhasse, o 
estado final recebia peso 1 (indicando vitória), redistribuído aos estados anteriores com um 
fator de propagação, que diminui conforme se aproxima do estado inicial. Importante 
destacar que os estados são diferenciados pelo jogador do turno, atribuindo-se o 
complemento da recompensa aos estados onde o jogador 2 atuou, gerando assim dados 
para ambos os jogadores no mesmo jogo.
A ideia é que, à medida que novos modelos sejam criados, eles joguem uns contra os 
outros (modelo 1 vs modelo 1, por exemplo), gerando um dataset superior ao anterior e, 
consequentemente, um modelo subsequente com melhor aprendizado sobre o jogo

## Amazons
É um jogo de tabuleiro composto por um tabuleiro de 10x10 posições e dois jogadores com 
quatro peças cada. Estas peças têm o movimento da Dama do jogo de damas (ou a 
combinação entre Torre e Bispo do xadrez), ou seja, move-se quantas posições desejar nas 
oito direções possíveis, além de poder lançar uma lança, em qualquer direção seguindo a 
mesma lógica de seu movimento, estas lanças não são removidas durante o jogo. O 
objetivo do jogo é imobilizar todas as peças do adversário.

## Definição do Estado de Jogo
Para facilitar e agilizar no processo de treinamento e aprendizado o espaço de estados do 
jogo foi reduzido pela metade, agora sendo representado por um tabuleiro de 5x5 posições 
e 2 damas para cada jogador. Um estado de jogo é a representação de uma das possíveis 
combinações de jogo no tabuleiro e as peças posicionadas sobre ele, sendo esse 
representado por um vetor de números. O array representante é composto pela 
concatenação de 76 valores: 
• Um número inteiro na primeira posição para representar o jogador do estado 
representado (1 para jogador 1 e 0 para jogador 2). 
• Um vetor que representa as posições de peças do jogador 1, em formato one-hot 
encoding (1 para posições em que há peça do jogador e 0 para as posições em que 
não há).
• Um vetor que representa as posições de peças do jogador 2, em formato one-hot 
encoding (1 para posições em que há peça do jogador e 0 para as posições em que 
não há).
• Um vetor que representa as posições de flexas, em formato one-hot encoding (1 
para posições em que há flexa posicionada e 0 para as posições em que não há).

## Sobre o Modelo
A nossa rede pode ser descrita da seguinte forma:
• Camada Linear: 76 entradas -> 76 saídas
a. Ativação ReLU
b. Normalização em lote
c. Dropout: 20% de taxa
• Camada Linear: 76 entradas -> 50 saídas
d. Ativação ReLU
e. Normalização em lote
f. Dropout: 20% de taxa
• Camada Linear: 50 entradas -> 25 saídas
g. Ativação ReLU
h. Normalização em lote
i. Dropout: 20% e taxa
Camada Linear: 25 entradas -> 1
- Utilizamos a ativação ReLU nas camadas ocultas e uma ativação linear na saída, 
necessária para representar a pontuação do jogador atual.
- Para prevenir o sobreajuste, empregamos dropout e normalização em lote. 
Observamos alta correlação entre os estados de uma partida, indicando potencial 
para grande overfitting sem uma amostra expressiva de estados visitados.
- O paper do AlphaGo evita repetição de estados no treinamento, abordagem que 
não aprofundamos por considerá-la além do escopo do projeto.
