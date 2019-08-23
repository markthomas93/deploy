# jupyter.presentation

```bash
docker run \
-v $PWD/:/home/jovyan/ \
-p 80:8888 \
--name debug.ubuntu.jupyter.base.notebook.dev \
-it --rm markthomas93/jupyter:latest /bin/sh
```