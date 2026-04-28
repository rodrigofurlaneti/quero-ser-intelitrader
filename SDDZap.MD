Script de Prompt SDD (Arquitetura e Implementação)
Copie e adapte o prompt abaixo para iniciar o desenvolvimento de cada módulo:

Atue como um Arquiteto de Sistemas Sênior. Vamos resolver o desafio técnico de integração Android Real-Time via Redis.

1. Visão Geral da Solução
Agente Nativo (Android): Desenvolvido em Rust (pela segurança de memória e performance) rodando via NDK.

Middleware: Redis (usando Streams para mensagens e Pub/Sub para comandos de contatos).

Backend: .NET 9 seguindo Clean Architecture e CQRS.

2. Módulo Android (Rust + SQLite + Redis)
Preciso do design técnico e código para:

Monitoramento O(1): Como monitorar o msgstore.db do WhatsApp Business sem onerar a CPU. Considere o uso de inotify no arquivo -wal ou um polling inteligente baseado no last_rowid da tabela messages.

Concorrência: Uso de tokio ou async-std em Rust para manter duas threads: uma de leitura (SQLite -> Redis) e uma de escuta (Redis -> Android Contacts).

Escrita de Contatos: Script para executar content insert via std::process::Command no Android para inserir o JSON recebido do Redis na agenda do sistema.

3. Módulo .NET (.NET 9 + StackExchange.Redis)
Desenvolva a estrutura seguindo meus princípios:

Domain: Entidades WhatsAppMessage e SystemContact.

Infrastructure: Implementação de um RedisConsumer (IHostedService) para ler mensagens em tempo real e um RedisPublisher para enviar contatos.

API: Controller minimalista com endpoint POST /contacts.

4. Resiliência e Segurança
Como garantir que, se o Agente Nativo perder conexão com o Redis, ele não perca mensagens do SQLite que ocorreram durante o downtime? (Estratégia de Checkpointing).

Tratamento de erros para permissões de acesso ao /data/data/com.whatsapp.w4b/.

Pontos Chave para a sua Tomada de Decisão
Para brilhar no vídeo e na entrevista técnica, considere estas definições que alinhei com seu perfil:

A. O Desafio do SQLite (Ação A)
O WhatsApp usa WAL Mode. Isso significa que as mensagens novas entram primeiro no arquivo msgstore.db-wal.

Decisão Técnica: Em vez de ler o banco inteiro, você deve abrir a conexão em modo ReadOnly e buscar apenas registros onde _id > last_processed_id. Isso garante a performance que você busca.

B. Inserção de Contatos (Ação B)
No Android, inserir um contato via binário nativo (sem ser uma App Java/Kotlin formal) exige interagir com o content provider do sistema.

Comando Sugerido: ```bash
content insert --uri content://com.android.contacts/raw_contacts --bind account_name:s:PROJETO_DESAFIO --bind account_type:s:com.google


C. A Camada .NET (Sua Especialidade)
Aplique o que você está fazendo no seu projeto Finance Manager:

Use Dependency Injection rigorosa.

Configure o Redis com AbortOnConnectFail = false para que a API não quebre se o broker demorar a subir.

Considere usar Records do C# para os DTOs de mensagem, garantindo imutabilidade.
