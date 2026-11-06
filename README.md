<div align="center">
  <img width="400" height="200" alt="Image" src="https://github.com/user-attachments/assets/2a715a7c-3a6d-4529-bcce-6048c5b2034d" />
</div>

# Project-Arm-Robotic

Braço robótico de automação, **totalmente impresso em 3D**, voltado para a **agricultura** (colheita e debulha de milho).

Este repositório reúne todas as atualizações do projeto: documentação, códigos e decisões de projeto. A modelagem 3D, a documentação, os códigos e os materiais escolhidos partem do repositório oficial dos desenvolvedores do projeto **Thor**, adaptados aqui para o uso agrícola.

> Projeto Thor (base deste trabalho): https://github.com/AngelLM/Thor
> Documentação do Thor: http://thor.angel-lm.com/documentation/

## Sumário

- [Status do projeto](#status-do-projeto)
- [Motivação](#motivação)
- [Especificações](#especificações)
- [Materiais](#materiais)
- [Efetuador de debulha](#efetuador-de-debulha)
- [Eletrônica e firmware](#eletrônica-e-firmware)
- [Fluxo de funcionamento](#fluxo-de-funcionamento)
- [Visão computacional](#visão-computacional)
- [Pontos em análise](#pontos-em-análise)
- [Roadmap](#roadmap)
- [Estrutura do repositório](#estrutura-do-repositório)
- [Como contribuir](#como-contribuir)
- [Créditos e licença](#créditos-e-licença)
- [Autoria](#autoria)

## Status do projeto

🚧 **Em desenvolvimento (fase de projeto).** O fluxo de funcionamento já foi diagramado e os componentes foram levantados. O protótipo ainda não foi montado nem testado.

## Motivação

O Thor padrão é um excelente ponto de partida para entender a mecânica de um braço articulado de 6 eixos, mas a **carga útil de 500 g a 750 g** e o **NEMA 17 do pulso** são insuficientes para colher e debulhar milho. Por isso este projeto propõe um **efetuador dedicado** (debulhadora) no lugar da garra padrão, além de uma revisão dos motores.

## Especificações

Valores do Thor padrão (referência para este projeto):

- **Eixos:** 6 (6 DOF), articulado, com motores de passo e correias GT2
- **Alcance máximo:** aproximadamente 625 mm (da base ao pulso, sem contar a garra)
- **Altura total:** 800 mm a 900 mm com o braço estendido na vertical
- **Segmentos (links):** largura média de 80 mm a 100 mm, ocos para a passagem de cabos, com paredes internas reforçadas
- **Carga útil:** 500 g a 750 g com extensão total
- **Garra padrão (tipo pinça):** abertura de 50 mm a 70 mm, comprimento de cerca de 120 mm e dedos de 15 mm a 20 mm de espessura, com furos para borrachas ou sensores de pressão
- **Alimentação:** fonte 12 V / 15 A
- **Controle:** Arduino Mega 2560 + shield RAMPS 1.4

> Os valores finais do Arm-bot-agro (com o efetuador de debulha) serão atualizados aqui após os testes.

## Materiais

| Qtd. | Item | Uso |
|---|---|---|
| 3 | Motor de passo NEMA 23 (4,2 kgf·cm ou superior) | Eixos da base, ombro e cotovelo |
| 3 | Motor de passo NEMA 17 (mínimo 4,0 kgf·cm) | Eixos do pulso |
| 6 | Drivers de motor de passo TMC2208 ou TMC2209 | Um por eixo |
| 5 m | Correia GT2 (6 mm de largura) | Transmissão |
| 6 | Polias GT2 de 20 dentes | Eixos dos motores |
| 1 pacote | 20 rolamentos 608ZZ | Juntas |
| 1 kit | Parafusos e porcas M3, M4 e M5 (cabeça Allen) | Montagem |
| 1 | Arduino Mega 2560 | Controlador |
| 1 | Shield RAMPS 1.4 | Interface dos drivers |
| 1 | Fonte de alimentação 12 V / 15 A | Energia |
| 1 | Motor DC com redução JGB37-520 (12 V, 300 a 500 RPM) | Debulhadora |
| 1 | Módulo MOSFET driver LR7843 | Controle da debulhadora (PWM) |
| 1 | Acoplador de eixo rígido (6 mm para 6 mm) | Debulhadora |
| — | Endstops ópticos | Homing (mais resistentes à poeira que os mecânicos) |
| — | Peças impressas em 3D | Estrutura, polias e amortecedor de TPU |

Links de referência: [NEMA 23](https://www.robocore.net/motor-caixas-de-reducao/motor-de-passo-nema23-12kgf) · [NEMA 17](https://www.robocore.net/motor-caixas-de-reducao/motor-de-passo-nema17-4kgf)

## Efetuador de debulha

Para usar o método da debulhadeira, a garra do 6º eixo é substituída por um **efetuador dedicado**. Duas soluções estão em análise:

### Opção 1: polias e correia GT2 (redução paralela)

Solução mais barata e simples de fabricar digitalmente. A correia absorve bem os trancos da debulha, sem quebrar dentes de engrenagem. Mantém o NEMA 17.

- **Polia motora:** GT2 de alumínio, 16 ou 20 dentes
- **Polia movida (rotor debulhador):** GT2 impressa em 3D (PETG ou ABS), 60 ou 80 dentes
- **Correia:** GT2 de borracha com núcleo de fibra de vidro, em loop fechado (110 mm a 200 mm, conforme o espaçamento do projeto)
- **Relação 1:4** (20 para 80 dentes): multiplica o torque teórico para cerca de **16 kgf·cm**, reduzindo a velocidade na mesma proporção, o que ajuda a preservar a integridade dos grãos

### Opção 2: motor DC com redução

- **Série JGB37-520 (12 V ou 24 V):** caixa de engrenagens metálica, 37 mm de diâmetro. Versão com 300 a 600 RPM, torque contínuo de 4 a 6 kgf·cm e *stall torque* acima de 15 kgf·cm
- **Série JGA25-370:** alternativa mais compacta (25 mm), versão de 400 RPM (12 V), para grãos e vagens menores
- **Controle unidirecional:** módulo MOSFET (IRF520 ou LR7843), com velocidade por PWM
- **Controle bidirecional:** ponte H BTS7960 (43 A), que permite inverter o sentido para liberar um sabugo travado. O motor exige de 3 A a 5 A sob carga; L298N superaquece e não é recomendado

O fluxo atual do projeto usa o **motor DC a 400 RPM acionado por MOSFET**, com **amortecedor de TPU** para absorver a vibração.

## Eletrônica e firmware

- **Cérebro:** Arduino Mega 2560 com shield RAMPS 1.4 (comum em impressoras 3D), ou drivers de passo independentes
- **Firmware:** Marlin (adaptado) ou controle próprio escrito em C, para maior desempenho
- **ROS:** existem pacotes prontos para o Thor no GitHub, que permitem usar planejamento de trajetória com o MoveIt!
- **Sensores:** endstops ópticos para o *homing*

## Fluxo de funcionamento

1. **Energização:** a fonte de 12 V / 15 A alimenta a RAMPS e o Arduino
2. **Calibração:** *homing* nos endstops ópticos
3. **Espera:** aguarda a coordenada do alvo
4. **Cálculo:** cinemática inversa e geração de rampa de aceleração suave
5. **Movimento:** sinais para os drivers TMC2209; os NEMA 23 posicionam o braço XYZ e os NEMA 17 alinham o ângulo do pulso
6. **Verificação:** o alvo foi alcançado? Se não, repete o movimento
7. **Debulha:** o Arduino envia PWM ao MOSFET, o motor DC gira o tambor a 400 RPM e o amortecedor de TPU absorve a vibração
8. **Fim:** o MOSFET corta a energia, o braço calcula a rota de retorno e volta a aguardar um novo alvo

> Inclua aqui a imagem do diagrama (ex.: `docs/fluxograma.png`).

## Visão computacional

Proposta: uma **câmera na debulhadora** e uma rede neural para classificar o milho, usando um **ViT (Vision Transformer) do Hugging Face** com *fine-tuning* em imagens próprias.

- Modelos candidatos: `google/vit-base-patch16-224`, `facebook/deit-small-patch16-224`, `google/mobilevit-small`
- O Arduino não executa o modelo: um computador de borda (ex.: Raspberry Pi) processa a imagem e envia o resultado ao Arduino por serial
- Classificar não é localizar: para obter coordenadas do alvo, é preciso detecção (ex.: YOLO) ou outra estratégia, ainda a definir

## Pontos em análise

- **Motores:** o orientador indicou que os motores atuais são fracos. Revisar o torque dos NEMA 23 (com redução planetária, se necessário) e a corrente dos drivers (o TMC2209 entrega no máximo cerca de 2 A RMS)
- **Alimentação:** avaliar a passagem de 12 V para 24 V
- **Efetuador:** decidir entre a Opção 1 (correia GT2) e a Opção 2 (motor DC com redução)
- **Visão:** definir a posição da câmera e o método de localização do alvo

## Roadmap

- [x] Levantamento do projeto Thor e dos componentes
- [x] Diagrama do fluxo de funcionamento
- [ ] Revisão e dimensionamento dos motores
- [ ] Impressão 3D das peças
- [ ] Montagem mecânica
- [ ] Montagem eletrônica (RAMPS, drivers, endstops)
- [ ] Firmware e cinemática inversa
- [ ] Projeto e montagem do efetuador de debulha
- [ ] Treinamento e integração do modelo ViT
- [ ] Testes com milho e ajustes
- [ ] Documentação final

## Estrutura do repositório

Sugestão de organização (ajuste conforme o que existir no repositório):

```
Project-Arm-Robotic/
├── docs/        # documentação, diagramas e imagens
├── hardware/    # modelos 3D (STL/STEP) e desenhos
├── firmware/    # código do Arduino / Marlin adaptado
├── vision/      # treinamento e inferência do modelo ViT
├── electronics/ # esquemas de ligação
└── README.md
```

## Como contribuir

Sugestões, correções e ideias são bem-vindas. Abra uma *issue* descrevendo o problema ou a proposta, ou envie um *pull request* com as alterações.

## Créditos e licença

Este projeto é baseado no **Thor**, criado por Ángel LM e sua comunidade (https://github.com/AngelLM/Thor). Modelos 3D, documentação, códigos e materiais de origem pertencem aos autores originais e seguem a licença do repositório oficial. Consulte a licença do Thor antes de redistribuir ou usar comercialmente.

Licença das modificações deste repositório: *a definir*.

## Autoria

- **Autor:** Yan, Universidade Federal de Roraima (UFRR)
- **Orientador:** *[nome do orientador]*
- **Contato:** *[e-mail ou perfil]*
