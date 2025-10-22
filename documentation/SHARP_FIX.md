# Solução para Problema do Sharp no Docker

## Problema
O servidor wppconnect estava falhando com o erro:
```
Error: Could not load the "sharp" module using the linuxmusl-arm64 runtime
ERR_DLOPEN_FAILED: Error loading shared library libvips-cpp.so.8.17.1: No such file or directory
```

## Causa
O problema ocorre porque o Sharp não consegue carregar as bibliotecas necessárias no ambiente Alpine Linux com musl libc, especialmente em arquiteturas ARM64.

## Soluções Implementadas

### 1. Dockerfile Atualizado
- Adicionado `vips` (biblioteca principal) além de `vips-dev`
- Incluído `python3` e `py3-pip` para compilação nativa
- Reinstalação do Sharp no estágio final para garantir compatibilidade

### 2. Package.json Atualizado
- Adicionado `sharp: "^0.33.5"` como dependência explícita
- Garantindo que a versão correta seja instalada

### 3. Dockerfile Alternativo
Criado `Dockerfile.alternative` com configurações mais robustas:
- Configuração específica de hosts para binários do Sharp
- Teste de funcionamento do Sharp após instalação
- Remoção e reinstalação forçada do Sharp

## Como Usar

### Opção 1: Dockerfile Padrão (Recomendado)
```bash
docker-compose up --build
```

### Opção 2: Dockerfile Alternativo
Se ainda houver problemas, use o Dockerfile alternativo:
```bash
docker build -f Dockerfile.alternative -t wppconnect-server .
docker run -p 21465:21465 wppconnect-server
```

## Verificação
Para verificar se o Sharp está funcionando corretamente:
```bash
docker exec -it wpp-server node -e "const sharp = require('sharp'); console.log('Sharp version:', sharp.versions);"
```

## Dependências do Sistema Necessárias
- `vips` - Biblioteca principal de processamento de imagem
- `vips-dev` - Headers de desenvolvimento
- `fftw-dev` - Biblioteca FFTW
- `gcc`, `g++`, `make` - Compiladores
- `libc6-compat` - Compatibilidade com glibc
- `python3`, `py3-pip` - Para compilação nativa

## Troubleshooting
Se o problema persistir:
1. Verifique se todas as dependências do sistema estão instaladas
2. Use o Dockerfile alternativo
3. Considere usar uma imagem base diferente (ex: `node:22.20.0-slim`)
4. Verifique se não há conflitos de versão do Node.js
