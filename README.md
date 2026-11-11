# Carteira Digital

## 🚀 Como subir o ambiente local

Para preparar e inicializar a infraestrutura e a aplicação localmente, execute os seguintes passos:

1. **Configurar variáveis de ambiente:**

   ```bash
   cp .env.example .env
   ```

2. **Subir o banco de dados PostgreSQL:**

    ```bash
    docker compose up -d
    ```

3. **Iniciar o serviço contas:**
    ```bash
    ./mvnw spring-boot:run -pl contas
    ```
