FROM nginx:alpine

ARG IMAGE_TAG=v0.0.0
ARG BACKGROUND_COLOR=#e0f7fa

COPY index.html /usr/share/nginx/html/index.html

# Változók behelyettesítése az HTML fájlba
RUN sed -i "s/{{IMAGE_TAG}}/${IMAGE_TAG}/g" /usr/share/nginx/html/index.html && \
    sed -i "s/{{BACKGROUND_COLOR}}/${BACKGROUND_COLOR}/g" /usr/share/nginx/html/index.html

EXPOSE 80