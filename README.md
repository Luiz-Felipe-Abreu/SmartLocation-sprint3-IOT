# SmartLocationYolo – Sistema de Monitoramento e Detecção de Motocicletas com YOLO

![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)
![YOLO](https://img.shields.io/badge/YOLO-v8-green.svg)
![Oracle](https://img.shields.io/badge/Database-Oracle-red.svg)
![OpenCV](https://img.shields.io/badge/OpenCV-4.x-orange.svg)

## Desafio da Mottu
A Mottu lançou o desafio de desenvolver uma solução capaz de monitorar motos em tempo real, utilizando IoT e/ou Visão Computacional.  
O objetivo principal é identificar onde as motos estão localizadas e gerar informações que ajudem a entender a concentração delas em determinados pontos.

---

## Nossa Solução
Desenvolvemos um sistema que:
- Detecta múltiplas motos em vídeo usando YOLOv8.
- Realiza rastreamento contínuo dos alvos com ByteTrack.
- Registra posições e gera um mapa 2D de concentração (heatmap / scatter).
- Persiste dados relevantes em banco Oracle para análises posteriores.
- Gera outputs analíticos (vídeo anotado, CSV e gráficos).

---

## Vídeo Demonstrativo
[Assista à apresentação no YouTube](https://youtu.be/uXk0kMAu5g4)

---

## Tecnologias Utilizadas

### Inteligência Artificial & Visão Computacional
- YOLOv8 (Ultralytics) — detecção em tempo real.  
  Suporte a modelos: `yolov8n.pt`, `yolov8s.pt`, `yolov8m.pt`, `yolov8l.pt`, `yolov8x.pt`.  
- Detecção configurada para a classe **motorcycle** (COCO ID 3).  
- ByteTrack — rastreamento contínuo de objetos entre frames.

### Processamento de Dados
- OpenCV — leitura e processamento de vídeo, desenho de bounding boxes.  
- NumPy — operações numéricas e manipulação de arrays.  
- Pandas — estruturação e manipulação das tabelas de detecções.

### Visualização e Análise
- Matplotlib — scatter plots, heatmaps, histogramas e gráficos de pizza.
- Gráficos usados:
  - Scatter plots para mapeamento espacial.
  - Heatmap para concentração espacial.
  - Histogramas e gráficos de pizza para distribuições e análises por região.

### Banco de Dados
- Oracle Database — persistência das detecções.  
- oracledb — driver Python para conexão e inserção de dados.

### Ambiente de Desenvolvimento
- Jupyter Notebook — desenvolvimento interativo e organização por células.  
- PyTorch — backend do YOLO (quando aplicável).  
- CUDA — aceleração GPU quando disponível.

---

## Estrutura do Código (explicado por blocos/células)

O projeto foi implementado em um notebook Jupyter (`.ipynb`) organizado em células. Cada célula representa um passo do pipeline:

1. **Importação de bibliotecas**
   - Importa `cv2`, `ultralytics` (YOLO), `numpy`, `pandas`, `matplotlib`, `oracledb`, ByteTrack e utilitários.
   - Verifica versões e dependências.

2. **Carregamento do vídeo de entrada**
   - Detecta/abre o arquivo de vídeo com `cv2.VideoCapture`.
   - Lê metadados: FPS, largura, altura, total de frames.

3. **Configuração do modelo de detecção (YOLO)**
   - Carrega o modelo selecionado (ex.: `yolov8n.pt`) via API Ultralytics.
   - Ajusta parâmetros: `conf_threshold`, `iou_threshold`, classes (filtra `motorcycle`).
   - Teste rápido de inferência em frame de exemplo.

4. **Processamento dos frames do vídeo**
   - Loop principal que lê cada frame, executa inferência YOLO e recebe bounding boxes + scores.
   - Integração com ByteTrack para atribuir IDs persistentes às motocicletas.
   - Desenha bounding boxes, IDs e scores no frame para visualização.

5. **Coleta e armazenamento das posições**
   - Extrai coordenadas centrais dos bounding boxes (x, y).
   - Converte coordenadas de pixel para espaço 2D utilizado no mapa (simulação de lat/long, se aplicável).
   - Popula um `DataFrame` com: `frame_id`, `id_moto`, `pos_x`, `pos_y`, `confiança`, `timestamp`.

6. **Geração do mapa 2D de concentração**
   - Usa os pontos coletados para gerar:
     - Scatter plot das posições.
     - Heatmap (mapa de calor) de densidade.
     - Histogramas e gráficos de pizza para análises complementares (distribuição por região ou faixa horária).

7. **Exibição e salvamento dos resultados**
   - Renderiza o vídeo processado em tela (opcional) e escreve um arquivo MP4 com anotações.
   - Exporta CSV com todas as detecções.
   - Salva os gráficos em PNG/PDF.
   - Opcional: persiste registros no banco Oracle.

---

## Execução Passo a Passo (Células do Notebook)

**Célula 1: Instalação e Setup**
- Instala dependências (via `pip` se necessário).
- Importa bibliotecas essenciais.
- Verifica disponibilidade de CUDA/GPU e configura dispositivo (CPU/GPU).

**Célula 2: Carregamento do Modelo**
- Carrega o modelo YOLOv8 pré-treinado.
- Opções suportadas: `yolov8n.pt`, `yolov8s.pt`, `yolov8m.pt`, `yolov8l.pt`, `yolov8x.pt`.
- Configura `conf_threshold` e lista de classes (filtrar `motorcycle`).

**Célula 3: Seleção do Vídeo**
- Detecta automaticamente vídeos na pasta do projeto (`data/`).
- Formatos suportados: `.mp4`, `.mov`, `.mkv`, `.avi`, `.webm`.
- Permite seleção manual via variável/param.

**Célula 4: Funções Utilitárias**
- Funções auxiliares: leitura/escrita de CSV, timestamping, logging.
- Algoritmo de deduplicação por proximidade (remover detecções muito próximas no mesmo frame).
- Configuração do dispositivo (CPU/GPU) e funções de conversão de coordenadas.

**Célula 5: Processamento Principal**
- Detecção e rastreamento das motocicletas (YOLO + ByteTrack).
- Atualização do `DataFrame` de detecções em tempo real.
- Análise espacial parcial (cálculo de clusters, contagens por região).
- Geração de visualizações interativas / estáticas.

**Célula 6: Persistência no Oracle**
- Pré-processamento dos registros únicos para inserção.
- Inserção em lote no Oracle usando `oracledb`.
- Validação de inserções e listagem dos registros persistidos.

---

## Resultados e Métricas

### Métricas de Performance
- **Frames Processados**: contagem total de frames analisados.  
- **Motocicletas Únicas**: número de IDs únicos detectados e rastreados.  
- **Taxa de Detecção**: proporção de frames com detecções / frames totais.  
- **Confiança Média**: média das confidências das detecções.  
- **FPS Médio**: taxa média de processamento (frames por segundo).

### Outputs Gerados
- **Vídeo Anotado**: MP4 com bounding boxes e IDs das motocicletas.  
- **CSV Detalhado**: dataset com todas as detecções (`frame_id`, `id_moto`, `pos_x`, `pos_y`, `confiança`, `timestamp`).  
- **Gráficos Analíticos**: pelo menos 3 visualizações (scatter/heatmap, histograma, gráfico de pizza).  
- **Banco Oracle**: dados persistidos para consultas e análises futuras.

---

## Tabela Oracle: DETECCOES_MOTO

Script SQL sugerido para criação da tabela de detecções:

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

---

## Participantes
- Luiz Felipe Abreu - RM 555197
- Pedro Gomes - RM 553907
- Matheus Munuera - RM 557812