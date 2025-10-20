FROM nginx:alpine

# Install curl for healthchecks (optional)
RUN apk add --no-cache curl

# Set working directory
WORKDIR /var/www/html

# Expose ports
EXPOSE 80 443

# Start nginx in foreground
CMD ["nginx", "-g", "daemon off;"]
