# SmartLocation - Sistema de Detecção de Motocicletas com YOLO

![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)
![YOLO](https://img.shields.io/badge/YOLO-v8-green.svg)
![Oracle](https://img.shields.io/badge/Database-Oracle-red.svg)
![OpenCV](https://img.shields.io/badge/OpenCV-4.x-orange.svg)

## 📋 Descrição do Projeto

O **SmartLocation** é um sistema inteligente de detecção e rastreamento de motocicletas em tempo real, desenvolvido para monitoramento de estacionamentos e controle de acesso de veículos. Utilizando tecnologia de visão computacional avançada com YOLOv8 e integração com banco de dados Oracle, o sistema oferece análise completa das posições e movimentações de motocicletas.

### 🎯 Objetivos Principais

- ✅ **Detecção Automática**: Identificar motocicletas em vídeos com alta precisão
- ✅ **Rastreamento Inteligente**: Acompanhar motocicletas individuais ao longo do tempo
- ✅ **Análise Espacial**: Mapear distribuição de motocicletas por regiões do pátio
- ✅ **Persistência de Dados**: Armazenar dados no Oracle Database para análises futuras
- ✅ **Visualização Interativa**: Gerar gráficos e relatórios analíticos detalhados

## 🛠️ Tecnologias Utilizadas

### Inteligência Artificial & Visão Computacional
- **YOLOv8 (Ultralytics)**: Modelo de detecção de objetos em tempo real
  - Suporte para modelos: nano, small, medium, large, xlarge
  - Detecção específica para classe "motorcycle" (COCO ID 3)
  - ByteTrack para rastreamento contínuo de objetos

### Processamento de Dados
- **OpenCV**: Processamento de vídeo e manipulação de imagens
- **NumPy**: Operações matemáticas e manipulação de arrays
- **Pandas**: Análise e manipulação de dados estruturados

### Visualização e Análise
- **Matplotlib**: Geração de gráficos e visualizações interativas
  - Scatter plots para mapeamento espacial
  - Histogramas para distribuição estatística
  - Gráficos de pizza para análise regional

### Banco de Dados
- **Oracle Database**: Sistema de gerenciamento de banco de dados
- **oracledb**: Driver Python para conexão com Oracle

### Ambiente de Desenvolvimento
- **Jupyter Notebook**: Interface interativa para desenvolvimento e análise
- **PyTorch**: Framework de deep learning (backend para YOLO)
- **CUDA**: Aceleração GPU para processamento rápido (quando disponível)

## 🏗️ Arquitetura do Sistema

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Vídeo Input   │───▶│   YOLOv8 Model   │───▶│   Detecções     │
│   (.mp4, .avi)  │    │   + ByteTrack    │    │   Raw Data      │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │                        │
                                ▼                        ▼
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│  Vídeo Output   │◀───│  Frame Drawing   │    │  Data Processing│
│  (Annotated)    │    │  & Annotation    │    │  & Filtering    │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                                        │
                                                        ▼
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│ Oracle Database │◀───│  Data Storage    │◀───│   Analytics &   │
│  DETECCOES_MOTO │    │  (Unique Bikes)  │    │  Visualization  │
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

## 📊 Estrutura de Dados

### Tabela Oracle: `DETECCOES_MOTO`
```sql
CREATE TABLE DETECCOES_MOTO (
    ID_DETECCAO      NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    ID_MOTO          NUMBER,
    POSICAO_X        NUMBER,
    POSICAO_Y        NUMBER,
    CONFIANCA        NUMBER(5,3),
    HORARIO_REGISTRO DATE DEFAULT SYSDATE
);
```

### DataFrame Principal (df_motos)
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `track_id` | int | ID único da motocicleta rastreada |
| `centro_x`, `centro_y` | int | Coordenadas do centro da detecção |
| `confianca` | float | Nível de confiança da detecção (0-1) |
| `area` | int | Área da bounding box em pixels |
| `largura`, `altura` | int | Dimensões da bounding box |
| `regiao` | string | Região da imagem (Superior/Inferior + Esquerda/Direita) |

## 🚀 Como Usar

### 1. Pré-requisitos

#### Dependências do Sistema
```bash
# Instalar Python 3.8+ e pip
python --version
pip --version
```

#### Bibliotecas Python
```bash
pip install ultralytics opencv-python matplotlib pandas numpy oracledb pyyaml requests
```

### 2. Estrutura de Arquivos
```
SmartLocationYolo/
├── SmartLocation.ipynb          # Notebook principal
├── video_motos.mp4             # Vídeo de entrada
├── yolov8m.pt                  # Modelo YOLO (baixado automaticamente)
├── runs/
│   └── track/                  # Vídeos processados
├── deteccoes_motos_completo.csv # Dados exportados
└── README.md                   # Este arquivo
```

### 3. Configuração do Banco Oracle
```python
# Configurações necessárias no notebook
USER = 'seu_usuario'
PASSWORD = 'sua_senha'
DSN = 'oracle.fiap.com.br/ORCL'
```

### 4. Execução Passo a Passo

#### Célula 1: Instalação e Setup
- Instala todas as dependências necessárias
- Importa bibliotecas essenciais
- Verifica disponibilidade de CUDA/GPU

#### Célula 2: Carregamento do Modelo
- Carrega modelo YOLOv8 pré-treinado
- Opções: yolov8n.pt, yolov8s.pt, yolov8m.pt, yolov8l.pt, yolov8x.pt

#### Célula 3: Seleção do Vídeo
- Detecta automaticamente vídeos na pasta do projeto
- Suporta formatos: .mp4, .mov, .mkv, .avi, .webm

#### Célula 4: Funções Utilitárias
- Algoritmo de deduplicação por proximidade
- Configuração de dispositivo (CPU/GPU)

#### Célula 5: Processamento Principal
- Detecção e rastreamento de motocicletas
- Análise espacial completa
- Geração de visualizações interativas

#### Célula 6: Persistência no Oracle
- Processamento de dados únicos
- Inserção no banco de dados
- Validação e listagem dos resultados

## 📈 Funcionalidades Detalhadas

### 🔍 Detecção e Rastreamento
- **Processamento Multi-frame**: Análise frame por frame do vídeo
- **Filtro de Proximidade**: Remove detecções duplicadas baseado em distância
- **Rastreamento Persistente**: Mantém IDs únicos usando ByteTrack
- **Configurações Otimizadas**:
  - `CONF = 0.01`: Limiar de confiança baixo para máximo recall
  - `IOU = 0.6`: Threshold IoU para Non-Maximum Suppression
  - `MAX_DET = 800`: Máximo de detecções por frame
  - `DEDUP_DIST = 60.0`: Distância mínima entre detecções (pixels)

### 📊 Análise Espacial
- **Divisão Regional**: Segmenta imagem em 4 quadrantes
- **Análise de Distribuição**: Calcula percentuais de ocupação por região
- **Clustering de Posições**: Agrupa posições similares para análise
- **Estatísticas Detalhadas**: Médias, mínimos, máximos por região

### 🎨 Visualizações Interativas

#### Gráfico 1: Mapa de Posições
- Scatter plot com coordenadas X,Y das motocicletas
- Colorização baseada no nível de confiança
- Grid cartesiano para referência espacial

#### Gráfico 2: Distribuição Regional
- Gráfico de pizza mostrando % de ocupação por região
- Cores personalizadas para cada quadrante
- Estatísticas numéricas integradas

#### Gráfico 3: Análise de Confiança
- Histograma da distribuição de confiança das detecções
- Linhas verticais para estatísticas (média, min, max)
- Informações detalhadas sobrepostas

### 💾 Integração com Oracle Database
- **Conexão Segura**: Utiliza credenciais FIAP Oracle Cloud
- **Transações Controladas**: Commits explícitos para consistência
- **Queries Otimizadas**: SELECTs eficientes para análise de dados
- **Tratamento de Erros**: Logs detalhados para debugging

## 📊 Resultados e Métricas

### Métricas de Performance
- **Frames Processados**: Contagem total de frames analisados
- **Motocicletas Únicas**: Número de IDs únicos detectados e rastreados
- **Taxa de Detecção**: Proporção frames com detecções vs. frames totais
- **Confiança Média**: Qualidade média das detecções realizadas

### Outputs Gerados
1. **Vídeo Anotado**: MP4 com bounding boxes e IDs das motocicletas
2. **CSV Detalhado**: Dataset completo de todas as detecções
3. **Gráficos Analíticos**: 3 visualizações interativas diferentes
4. **Banco Oracle**: Dados persistidos para consultas e análises futuras

### Análises Disponíveis
- **Distribuição Espacial**: Identificação de zonas de maior concentração
- **Padrões Temporais**: Variações de detecção ao longo do vídeo
- **Qualidade das Detecções**: Análise estatística de confiança
- **Métricas de Ocupação**: Densidade de motocicletas por região

## 🎯 Casos de Uso

### 🏢 Gerenciamento de Estacionamentos
- Monitoramento automatizado de vagas ocupadas
- Contagem em tempo real de veículos estacionados
- Análise de padrões de uso e ocupação

### 🔒 Segurança e Vigilância
- Detecção de motocicletas não autorizadas
- Rastreamento de veículos suspeitos
- Logs automáticos de entrada e saída

### 🚦 Análise de Tráfego
- Estudos de fluxo de veículos em áreas específicas
- Identificação de pontos de congestionamento
- Suporte ao planejamento urbano e de mobilidade

## 🔧 Configurações Avançadas

### Parâmetros do Modelo YOLO
```python
# Configurações de detecção
CONF = 0.01          # Limiar de confiança (0-1)
IOU = 0.6            # Threshold IoU para NMS
MAX_DET = 800        # Máximo de detecções por frame
CLASSES = [3]        # ID da classe motorcycle no COCO
IMG_SIZE = 1280      # Resolução de inferência
DEDUP_DIST = 60.0    # Distância mínima para deduplicação
```

### Configurações de Região
```python
# Divisão automática em quadrantes
meio_x, meio_y = w // 2, h // 2

# Classificação de regiões
regioes = [
    "Superior Esquerda",
    "Superior Direita", 
    "Inferior Esquerda",
    "Inferior Direita"
]
```

## 🚀 Expansões Futuras

### 🎯 Detecção Multi-classe
- Expansão para carros, caminhões, bicicletas
- Detecção de pedestres
- Identificação de objetos de segurança

### ⚡ Processamento em Tempo Real
- Integração com câmeras IP ao vivo
- Sistema de alertas automáticos
- Dashboard web interativo com Node-RED

### 🧠 Machine Learning Avançado
- Modelos customizados treinados para contexto específico
- Reconhecimento automático de placas
- Análise comportamental de veículos

### 🔗 APIs e Integrações
- REST APIs para consultas externas
- Webhooks para notificações automáticas
- Integração com sistemas de gestão existentes

## 🐛 Solução de Problemas

### Problemas Comuns

#### Erro de Conexão Oracle
```bash
# Verificar conectividade
ping oracle.fiap.com.br
telnet oracle.fiap.com.br 1521
```

#### Modelo YOLO não encontrado
```python
# O modelo será baixado automaticamente na primeira execução
# Certifique-se de ter conexão com internet
model = YOLO('yolov8m.pt')  # Baixa automaticamente se necessário
```

#### Erro de memória GPU
```python
# Usar CPU se GPU não tiver memória suficiente
DEVICE = 'cpu'
HALF = False
```

#### Vídeo não encontrado
```bash
# Certifique-se de que há um arquivo de vídeo na pasta do projeto
# Formatos suportados: .mp4, .mov, .mkv, .avi, .webm
```

## 📝 Logs e Debugging

### Logs Importantes
- Verificação de disponibilidade CUDA/GPU
- Contagem de frames processados
- Estatísticas de detecção por região
- Status de conexão com Oracle
- Validação de dados inseridos

### Debugging
```python
# Ativar logs verbosos do YOLO
verbose=True

# Verificar dados do DataFrame
print(df_motos.info())
print(df_motos.describe())

# Validar conexão Oracle
teste_tabela()
listar_dados()
```

## 👥 Contribuições

Este projeto foi desenvolvido como parte do Challenge 3 - Semestre 4 da FIAP. Contribuições são bem-vindas através de:

1. Fork do repositório
2. Criação de branch para features (`git checkout -b feature/nova-funcionalidade`)
3. Commit das mudanças (`git commit -am 'Adiciona nova funcionalidade'`)
4. Push para branch (`git push origin feature/nova-funcionalidade`)
5. Criação de Pull Request

## 📄 Licença

Este projeto é desenvolvido para fins acadêmicos na FIAP - Faculdade de Informática e Administração Paulista.

### Tecnologias Utilizadas
- **Ultralytics YOLOv8**: Framework de detecção de objetos
- **FIAP Oracle Cloud**: Infraestrutura de banco de dados
- **COCO Dataset**: Classes de objetos pré-treinadas

## 📞 Suporte

Para dúvidas e suporte:

- **Documentação**: Consulte este README e comentários no código
- **Issues**: Abra uma issue no repositório para reportar bugs
- **FIAP**: Contate o suporte técnico da instituição para questões de infraestrutura

---

**Desenvolvido por**: Equipe SmartLocation  
**Instituição**: FIAP - Faculdade de Informática e Administração Paulista  
**Projeto**: Challenge 3 - Sistema IoT com Visão Computacional  
**Semestre**: 4º Semestre  
**Ano**: 2024

---

## 🏆 Resultados Esperados

Ao final da execução completa do projeto, você terá:

✅ **Sistema de detecção funcionando** com vídeo processado  
✅ **Dados estruturados** salvos em Oracle Database  
✅ **Análises visuais interativas** com gráficos detalhados  
✅ **Relatório completo** de posicionamento de motocicletas  
✅ **Base sólida** para expansões futuras do sistema  

**🎯 O SmartLocation representa uma solução completa e profissional para monitoramento inteligente de veículos usando tecnologias de ponta em visão computacional e análise de dados.**