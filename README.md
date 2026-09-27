# UERJBotz — Combat Lab

O **Combat Lab** é uma calculadora da UERJBotz para estimar o comportamento de robôs de combate a partir dos parâmetros de motores, transmissão, rodas e arma rotativa. A ferramenta ajuda a comparar configurações de locomoção, avaliar o limite de aderência e calcular mordida (*bite*) e energia cinética.

Os cálculos são executados no navegador, com atualização dos resultados ao alterar os campos. A interface possui dois módulos: **Locomoção** e **Arma & Bite**.

## Como usar

1. Escolha um preset, **Killbot** ou **Blitzen**, como ponto de partida. O botão **Customizado** restaura os valores padrão para uma nova configuração.
2. Selecione a categoria de referência: Fairy, Ant ou Beetle. A seleção preenche a massa de referência; ajuste o campo **Massa real** com o peso do seu robô.
3. Preencha os parâmetros na aba **Locomoção** e acompanhe as estimativas de velocidade, rotação e tração.
4. Na aba **Arma & Bite**, informe os dados do conjunto rotativo e a velocidade relativa de aproximação.
5. Consulte os painéis de equações e o bloco **Hipóteses do modelo e referências** para interpretar os resultados.

Editar um parâmetro sai do preset e coloca a configuração em modo customizado. As alterações ficam na sessão da página: não são salvas automaticamente ao recarregar.

## Locomoção

### Acionamento e rotação

O módulo contempla dois tipos de transmissão:

| Acionamento | Funcionamento | Parâmetros principais |
| --- | --- | --- |
| Direta / Redução | O motor aciona o centro da roda, diretamente ou por transmissão. | RPM ou KV, diâmetro da roda e relação de redução G. |
| Tangenciada (Atrito) | Um pino ligado ao motor toca a roda e transmite movimento por atrito. | KV, bateria, diâmetro do pino e diâmetro da roda. |

Para motores **brushed**, informe o RPM de catálogo na saída do motoredutor, quando houver. A relação G representa apenas a redução adicional. Para motores **brushless**, a rotação livre é estimada por KV multiplicado pela tensão nominal da bateria, considerando 3,7 V por célula LiPo.

O **fator de rotação sob carga** representa a fração da rotação livre usada na estimativa. Por exemplo, 85% de 1.000 RPM resulta em 850 RPM. Esse fator deve ser ajustado com medições; não representa eficiência energética.

A redução pode ser informada diretamente ou calculada pelo número de dentes ou diâmetros das polias:

- **G = saída / entrada**: G = 2 significa duas voltas do motor para uma volta da saída.
- **G = 1**: transmissão direta.
- Para cálculo por diâmetro, use o diâmetro efetivo de transmissão; em polias dentadas, prefira o número de dentes.

### Resultados de velocidade

O painel apresenta velocidade livre e estimada sob carga, em m/s e km/h, além de RPM, velocidade angular e frequência da roda.

Na transmissão direta ou reduzida:

**v = π × D × RPM / (60 × G)**

Aqui, D é o diâmetro da roda em metros e RPM é a rotação de entrada da transmissão. Na transmissão tangencial ideal, a velocidade linear é **v = π × d × RPM / 60**, usando o diâmetro d do pino em metros. O diâmetro da roda determina seu RPM, mas não altera essa velocidade linear quando não há escorregamento.

### Tração e torque

Informe a massa real, a quantidade de motores, o coeficiente de atrito μ e a porcentagem do peso apoiada nas rodas motrizes. A opção 2WD/4WD representa o número de rodas tracionadas; a quantidade de motores é configurada separadamente.

O limite de força imposto pela aderência é:

**F = μ × m × g × fração de peso motriz**

O torque necessário para atingir esse limite é **T = F × r**, usando o raio da roda na transmissão direta/reduzida ou o raio do pino no acionamento tangencial.

- **Brushed:** informe o torque disponível por motor em kgf·cm, na saída do motoredutor e antes da redução adicional G. Sem esse dado, o torque disponível e sua razão ficam indisponíveis.
- **Brushless:** o modelo estima torque por **T ≈ Kt × I**, com **Kt ≈ 60/(2πKV)**. A interpretação depende da convenção de corrente do motor/controlador; corrente da bateria e corrente de fase não são equivalentes.

A **razão de torque** compara o torque total disponível com o necessário para patinar. Um valor igual ou superior a 1 indica patinagem possível no modelo ideal. Isso não garante ausência de stall ou aquecimento.

## Arma & Bite

Informe o tipo de motor, RPM ou KV, bateria, fator de rotação sob carga, transmissão por polias, diâmetro da arma, quantidade de dentes efetivos e momento de inércia.

A **velocidade relativa de aproximação** representa o fechamento da distância entre o robô e o alvo. O botão **Puxar da Tração** copia a velocidade estimada da locomoção naquele momento; mudanças posteriores na tração exigem uma nova cópia. Para um alvo em movimento, ajuste a velocidade relativa conforme a situação.

| Resultado | Significado |
| --- | --- |
| Mordida máxima | Avanço relativo entre duas passagens consecutivas dos dentes. |
| Média idealizada | Metade da mordida máxima, supondo fase de contato uniformemente distribuída. |
| Energia cinética | Energia armazenada na rotação da arma. |
| Velocidade de ponta | Velocidade tangencial no raio externo da arma. |
| Passagens por segundo | Quantidade de passagens dos dentes efetivos por segundo. |

As relações utilizadas são:

- **Bite máximo:** b = 60 × v / (n × RPM) × 1000, em mm; v em m/s e n é o número de dentes efetivos igualmente espaçados.
- **Velocidade angular:** ω = 2π × RPM / 60, em rad/s.
- **Energia:** E = ½ × I × ω², em joules.
- **Velocidade de ponta:** v = ω × r, em m/s.

O momento de inércia deve ser informado em **g·mm²**, em torno do eixo real de giro. A conversão para kg·m² utiliza o fator **10⁻⁹**. Confira a unidade e o eixo no CAD antes de copiar o valor. Com a arma parada, a mordida fica indefinida e é exibida como “—”.

## Interpretando as estimativas

Os presets são referências iniciais: confirme se os componentes e valores correspondem ao robô real. As faixas de velocidade e bite servem como referências internas de projeto, não como regras oficiais ou garantia de desempenho competitivo.

O modelo assume transmissão ideal, motores idênticos e distribuição equilibrada de carga. Não calcula aceleração, tempo de spin-up, aquecimento, queda de tensão ou comportamento detalhado do ESC. No acionamento tangencial, o pino pode escorregar sobre o pneu antes de a roda patinar no chão.

A mordida real também depende da geometria e do relevo do dente. A energia armazenada não é necessariamente transferida integralmente durante o impacto.

Campos inválidos são destacados e os resultados dependentes ficam indisponíveis até a correção. Valores zero são aceitos apenas nos parâmetros em que fazem sentido para o modelo.

## Estrutura do projeto

| Arquivo | Função |
| --- | --- |
| `index.html` | Interface, estilos personalizados e lógica dos cálculos. |
| `assets/tailwind.min.css` | Classes de estilo do Tailwind já compiladas. |
| `assets/logo-uerjbotz.jpg` | Logo completo da equipe, utilizado no rodapé e favicon. |
| `assets/nome-uerjbotz.png` | Nome oficial da equipe, utilizado no cabeçalho. |
| `README.md` | Documentação de uso e funcionamento. |
| `.nojekyll` | Marcador para publicação estática sem processamento Jekyll. |
| `.gitignore` | Regras para ignorar arquivos locais, caches e temporários. |

Os presets estão definidos em `ROBOT_PRESETS`, e as categorias e faixas de referência em `ROBOT_CATEGORIES`, no JavaScript do HTML. Os caminhos das imagens e estilos são relativos; mantenha a estrutura das pastas ao mover o projeto.

## Referências

- [Team Run Amok — Ask Aaron: spinners e bite](https://runamok.tech/AskAaron/spinner_FAQ.html)
- [maxon — Dados e simulação de motores](https://support.maxongroup.com/hc/en-us/articles/360013761160-Motor-data-and-simulation)
