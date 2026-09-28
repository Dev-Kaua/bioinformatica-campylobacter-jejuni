# Trabalho Final de Bioinformática — *Campylobacter jejuni*

IFSC Câmpus Gaspar · Análise e Desenvolvimento de Sistemas · Tópicos em Bioinformática (Prof. Dr. Renato Simões Moreira)

- **Aluno:** Kauã Reis Rodrigues
- **Organismo:** *Campylobacter jejuni* subsp. *jejuni* NCTC 11168 (Gram-negativa)
- **Proteoma de referência (UniProt):** [UP000000799](https://www.uniprot.org/proteomes/UP000000799), 1.623 proteínas

## Conteúdo

| Arquivo / pasta | Descrição |
|---|---|
| `relatorio.pdf` | Relatório do estudo de caso |
| `input.fasta` | Proteoma completo baixado do UniProt |
| `top50.fasta` | 50 primeiras proteínas de membrana (`results_fastprotein/raw/membranes.fasta`) |
| `results_fastprotein/` | Saída completa do FastProtein |
| `results_epibuilder/` | Saída completa do EpiBuilder (`--loc gram_neg`) |
| `fastprotein_log.txt`, `epibuilder_log.txt` | Logs das execuções (tempos de processamento) |

## Como foi executado

```bash
curl -s "https://rest.uniprot.org/uniprotkb/stream?format=fasta&query=proteome:UP000000799" -o input.fasta

docker run --rm -v ${PWD}:/data bioinfoufsc/fastprotein:clean-latest \
  fastprotein -i /data/input.fasta -o /data/results_fastprotein

awk '/^>/{c++} c<=50' results_fastprotein/raw/membranes.fasta > top50.fasta

docker run --rm -v ${PWD}:/data/ -v /var/run/docker.sock:/var/run/docker.sock \
  -v epibuilder-data:/tmp/epibuilder -e EPIBUILDER_VOLUME=epibuilder-data \
  bioinfoufsc/epibuilder-core epibuilder --input_file /data/top50.fasta \
  --loc gram_neg --output /data/results_epibuilder
```

## Resumo dos resultados

- FastProtein: 1.621 proteínas processadas, 2 ignoradas, tempo total de 4 min 19 s; 372 com domínio transmembranar e 383 com evidência de membrana.
- EpiBuilder: 9 das 50 proteínas com epítopos previstos (13 epítopos no total).
