FROM python:3.11.5

WORKDIR /usr/src/app

COPY requirements_with_cuda.txt ./
RUN pip install --upgrade pip
RUN pip install --no-cache-dir -r requirements.txt
RUN wandb login <ENTER_YOUR_WEIGHTSANDBIASES_API_KEY>

COPY distilBERT.py ./
COPY start.sh ./

RUN chmod +x start.sh

CMD ["./start.sh"]