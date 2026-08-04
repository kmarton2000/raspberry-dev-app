FROM arm32v6/alpine:3.23

ARG IMAGE_TAG=v0.0.0
ARG BACKGROUND_COLOR=#e0f7fa

# 1. Nginx telepítése és a szükséges könyvtárak létrehozása
RUN apk add --no-cache nginx && \
    mkdir -p /usr/share/nginx/html /run/nginx

# 2. HTML fájl másolása
COPY index.html /usr/share/nginx/html/index.html

# 3. Változók behelyettesítése és alapértelmezett Nginx gyökérkönyvtár beállítása
RUN sed -i "s/{{IMAGE_TAG}}/${IMAGE_TAG}/g" /usr/share/nginx/html/index.html && \
    sed -i "s/{{BACKGROUND_COLOR}}/${BACKGROUND_COLOR}/g" /usr/share/nginx/html/index.html && \
    sed -i "s|root /var/www/localhost/htdocs;|root /usr/share/nginx/html;|g" /etc/nginx/http.d/default.conf

EXPOSE 80

# 4. Nginx indítása előtérben (daemon off)
CMD ["nginx", "-g", "daemon off;"]