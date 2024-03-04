FROM nginx:alpine

# Copy config nginx
COPY conf /etc/nginx/conf.d/default.conf

WORKDIR /usr/share/nginx/html

# Remove default nginx static assets
RUN rm -rf ./*

# Copy static assets from builder stage

#Do we need this at all? Let us find out
# COPY --from=web-stack_frontend:latest /app/proxy .

# Containers run nginx with global directives and daemon off
ENTRYPOINT ["nginx", "-g", "daemon off;"]
