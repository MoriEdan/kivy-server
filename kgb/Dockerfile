FROM python:2.7.15-alpine

COPY requirements.txt /

RUN set -ex \
    && apk add --no-cache curl \
    && pip install --no-cache-dir -r requirements.txt \
    && mkdir kgb \
    && curl -Lf https://raw.githubusercontent.com/kivy/kgb2/master/main.py > /kgb/main.py \
    && apk del curl

EXPOSE 5000

CMD ["python", "/kgb/main.py"]
