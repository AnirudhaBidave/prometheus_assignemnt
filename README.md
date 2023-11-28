# prometheus_assignemnt
  1. Export the metrics (like request per second, memory usage, cpu usage etc) in the existing mini project given to Interns

  2. Install Prometheus and Grafana using Docker (with docker-compose)

  3. Configure prometheus (scrape configs) such way that it can scrape the metrics from default metric path of the application job

  4. Validate the entire configuration to check if the data is coming or not in Prometheus UI

  5. Create the Dashboards in Grafana on top of the metrics exported by adding the Prometheus as a Datasource.

### Create a docker-compose file
Create a docker-compose.yml file to deploy two docker container using prometheus and grafana images.<br>
And map the volumes like ```- ./prometheus.yml:/etc/prometheus/prometheus.yml```<br>
Command to run the contaner<br>
```sudo docker-compose up -d```

### Make some changes code
In our code we have to import ```prometheus-client```<br>
And create a url route from metrics function<br>
  ```python
    from prometheus_client import Counter, Gauge, generate_latest, REGISTRY
    from prometheus_client.exposition import MetricsHandler
    import psutil
    request_counter = Counter('django_requests_total', 'Total number of requests received')
    cpu_usage = Gauge('django_cpu_usage_percent', 'CPU usage percentage')

    def metrics(request):
        # Update CPU usage gauge
        cpu_percent = psutil.cpu_percent()
        cpu_usage.set(cpu_percent)
    
        # Generate metrics response
        response = HttpResponse(content_type='text/plain; version=0.0.4')
        response.write(generate_latest(REGISTRY))
        return response
```
### Build docker image
Build the docker image of which service you want to deploy on kube to monitor it.<br>
Command to build docker image<br>
```sudo docker build anirudhabidave/my-django-app .```

### Deploy the image on kube pod
We have to create a deployment.yml and service.yml file <br>
Deployment file to carte a pod and service file to map a port to it.<br>
Command to deploy pod and mount port<br>
```kubectl create -f deployment.yml```<br>
```kubectl create -f service.yml```

### Cofigure prometheus
To configure the prometheus we have to create a promethues.yml file and give a target to prometheus from where to export the metrics.<br>

### open prometheus and grafana web
We can access prometheus ```localhost:9090```<br>
Grafana on ```localhost:3000```

##### Validate the entire configuration to check if the data is coming or not in Prometheus UI
![Screenshot from 2023-11-28 12-38-07](https://github.com/AnirudhaBidave/prometheus_assignemnt/assets/130437908/42db9156-5ad9-4dce-be56-1ad7d540f4f1)

![Screenshot from 2023-11-28 12-41-07](https://github.com/AnirudhaBidave/prometheus_assignemnt/assets/130437908/fc1f1596-b793-4539-a514-7ca9c91668b0)

##### Create the Dashboards in Grafana on top of the metrics exported by adding the Prometheus as a Datasource.
![Screenshot from 2023-11-28 13-47-24](https://github.com/AnirudhaBidave/prometheus_assignemnt/assets/130437908/8b063a30-cb88-424c-b5c4-b29c6ed438e0)
![Screenshot from 2023-11-28 13-47-02](https://github.com/AnirudhaBidave/prometheus_assignemnt/assets/130437908/d93406a9-25df-47ed-852a-e5d7d91e828e)
