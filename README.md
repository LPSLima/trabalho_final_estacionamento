README.txt
Projeto Estacionamento Concorrente - Documentação Completa

========================================================
1. Objetivo do Projeto
========================================================

O projeto simula o gerenciamento de um estacionamento com múltiplas vagas, usando conceitos de programação concorrente e mecanismos de comunicação e sincronização entre processos e threads. 

O objetivo é demonstrar de forma prática:

- Processos e threads POSIX
- Exclusão mútua e semáforos
- Memória compartilhada
- Filas de mensagens POSIX (IPC)
- Coordenação de múltiplos agentes concorrentes (carros e sensores)

O sistema deve gerenciar a entrada e saída de carros, atualizar sensores em tempo real e exibir informações em um painel eletrônico.

========================================================
2. Funcionalidade do Projeto
========================================================

- Cada carro é um **processo independente**, que entra, ocupa uma vaga, permanece por algum tempo e depois sai.
- Threads sensores monitoram continuamente as vagas e registram informações em tempo real.
- O painel eletrônico recebe mensagens via fila POSIX e exibe as ações dos carros e o status das vagas.
- O manager encerra o sistema quando todos os carros finalizaram.
- As vagas livres são controladas por semáforos, garantindo que não ocorram condições de corrida.

========================================================
3. Arquitetura e Componentes do Sistema
========================================================

1. **common.h**: Define constantes, a estrutura da memória compartilhada (vagas, semáforos, mutex) e nomes para IPC.
2. **manager.c**: 
   - Inicializa memória compartilhada, semáforos, mutex e fila de mensagens.
   - Cria threads sensores para monitoramento.
   - Cria processos carros (fork + exec).
   - Aguarda finalização dos carros e encerra threads e IPC.
3. **car.c**: 
   - Cada carro ocupa uma vaga livre, envia mensagem para painel e depois libera a vaga.
   - Usa semáforos e mutex para garantir exclusão mútua.
4. **panel.c**: 
   - Recebe mensagens da fila POSIX e exibe no console as ações dos carros e status do estacionamento.
5. **Makefile**: 
   - Compila os arquivos `manager.c`, `car.c` e `panel.c`.
   - Permite limpar executáveis com `make clean`.

========================================================
4. Mecanismos de IPC e Sincronização
========================================================

| Mecanismo                     | Uso no Projeto |
|--------------------------------|----------------|
| Processos (fork + exec)       | Cada carro é um processo independente |
| Threads POSIX (pthread_create)| Sensores monitorando vagas em paralelo |
| Semáforos POSIX (sem_t)       | Controlar vagas livres e ocupadas (produtor-consumidor) |
| Mutex (sem_mutex)              | Exclusão mútua ao acessar memória compartilhada |
| Memória Compartilhada (shm_open + mmap) | Armazenamento global do estado das vagas |
| Filas de mensagens POSIX (mq_open, mq_send, mq_receive) | Comunicação de eventos para o painel |
| Sinais (SIGINT)                | Encerramento seguro de threads e processos |

========================================================
5. Como Executar
========================================================

1. Compile o projeto na pasta `src`:

   make

Isso gera os executáveis: `manager`, `car` e `panel`.

2. Abra dois terminais:

   - Terminal 1: execute o manager
     ./manager

   - Terminal 2: execute o painel
     ./panel

3. Observe a simulação:

   - Carros entrando e saindo das vagas
   - Sensores monitorando vagas livres
   - Painel mostrando mensagens em tempo real

4. Para limpar os executáveis:
   
   make clean

========================================================
6. Explicação do Funcionamento
========================================================

Exemplo de saída do sistema:

Carro 1 estacionou na vaga 0 (PID 2311)
Carro 2 estacionou na vaga 1 (PID 2312)
Carro 3 estacionou na vaga 2 (PID 2313)
[Car 1] saiu da vaga 0
Sensor 1 registrando carro... Vagas livres: 8
Carro 4 estacionou na vaga 0 (PID 2314)
Carro 5 estacionou na vaga 3 (PID 2315)
[Car 2] saiu da vaga 1
Sensor 3 registrando carro... Vagas livres: 7
[Car 3] saiu da vaga 2
Sensor 2 registrando carro... Vagas livres: 8
Carro 6 estacionou na vaga 1 (PID 2316)
[Car 4] saiu da vaga 0
Sensor 1 registrando carro... Vagas livres: 8
Carro 7 estacionou na vaga 0 (PID 2317)
[Car 5] saiu da vaga 3
Sensor 3 registrando carro... Vagas livres: 8
Carro 8 estacionou na vaga 2 (PID 2318)
[Car 6] saiu da vaga 1
Sensor 2 registrando carro... Vagas livres: 8
[Car 7] saiu da vaga 0
Sensor 1 registrando carro... Vagas livres: 9
[Car 8] saiu da vaga 2
Todos os carros finalizaram.
Sensor 3 registrando carro... Vagas livres: 10
Sensor 2 registrando carro... Vagas livres: 10
Manager encerrado.

**O que ocorre em cada linha:**

- "Carro X estacionou..." → processo carro ocupa vaga livre, mutex e semáforo controlam acesso.
- "[Car X] saiu..." → carro libera a vaga e atualiza memória compartilhada.
- "Sensor X registrando carro..." → thread sensor lê memória compartilhada em paralelo e mostra vagas livres.
- "Todos os carros finalizaram" → todos processos carros terminaram.
- "Manager encerrado" → threads sensores finalizam, fila de mensagens fechada e memória compartilhada liberada.

========================================================
7. Observações e Melhorias Possíveis
========================================================

- O projeto segue o modelo **produtor-consumidor** garantindo que não ocorra condições de corrida.
- Número de carros (`N_CARS`) e sensores (`N_WORKERS`) pode ser ajustado em `manager.c`.
- Sistema permite monitoramento em tempo real de todas as vagas do estacionamento.
- Para apresentação, pode-se mostrar **painel eletrônico e log do manager** simultaneamente.

========================================================
8. Estrutura do Projeto
========================================================

trabalho_final_estacionamento/
├── src/
│   ├── manager.c
│   ├── car.c
│   ├── panel.c
│   ├── common.h
│   ├── Makefile
├── README.txt
