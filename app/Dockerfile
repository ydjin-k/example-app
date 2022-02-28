FROM python:3.10.0-slim-buster
WORKDIR /usr/src/app
RUN groupadd --gid 2000 app \
    && useradd --uid 2000 --gid app --shell /bin/bash --create-home app \
    && chown app:app /usr/src/app
RUN apt-get update && apt-get install -y curl
COPY requirements.txt .
RUN pip3 install --no-cache-dir -r requirements.txt
EXPOSE 6363
COPY --chown=app:app . .
USER app
HEALTHCHECK --interval=5s --timeout=15s \
  CMD curl -f --fail http://localhost:6363 || exit 1
ENTRYPOINT [ "gunicorn" ]
CMD [ "--bind", "0.0.0.0:6363", "--reload", "js_example:app" ]
