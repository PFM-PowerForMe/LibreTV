FROM docker.io/library/alpine:edge AS git
RUN apk update && apk add --no-cache \
	git
WORKDIR /
RUN git -c advice.detachedHead=false clone --branch main --depth=1 --recurse-submodules --shallow-submodules https://github.com/pfm-powerforme/libretv.git source
WORKDIR /source/


FROM docker.io/library/node:lts-alpine AS runtime
ENV PORT=8080
ENV CORS_ORIGIN=*
ENV DEBUG=false
ENV REQUEST_TIMEOUT=5000
ENV MAX_RETRIES=2
ENV CACHE_MAX_AGE=1d
COPY --from=git /source/ /app/
WORKDIR /app
RUN npm ci --only=production && npm cache clean --force
EXPOSE 8080
HEALTHCHECK --interval=30s --timeout=10s --start-period=40s --retries=3 \
  CMD node -e "require('http').get('http://localhost:8080', (res) => { process.exit(res.statusCode === 200 ? 0 : 1) }).on('error', () => process.exit(1))"
CMD ["npm", "start"]