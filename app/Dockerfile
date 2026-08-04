# 1. STAGE: HTML fájl előkészítése AMD64-en (ez fut a Jenkins agenten)
FROM alpine:latest AS builder

ARG IMAGE_TAG=v0.0.0
ARG BACKGROUND_COLOR=#e0f7fa

WORKDIR /app
COPY index.html .

# Változók behelyettesítése
RUN sed -i "s/{{IMAGE_TAG}}/${IMAGE_TAG}/g" index.html && \
    sed -i "s/{{BACKGROUND_COLOR}}/${BACKGROUND_COLOR}/g" index.html

# 2. STAGE: Végső ARMv6 Nginx kép
FROM arm32v6/nginx:alpine

# Átmásoljuk az előkészített HTML-t az ARMv6 képre (itt nincs RUN parancs!)
COPY --from=builder /app/index.html /usr/share/nginx/html/index.html

EXPOSE 80