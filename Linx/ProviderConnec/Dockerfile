FROM docker.n8n.io/n8nio/n8n:latest

USER root

# Instala o Chromium e as fontes necessárias usando o gerenciador 'apk' do Alpine
RUN apk update && apk add --no-cache \
    chromium \
    nss \
    freetype \
    harfbuzz \
    ca-certificates \
    ttf-freefont

# Configura as variáveis de ambiente globais para o Puppeteer apontar para o local correto no Alpine
ENV PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true
ENV PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser

USER node