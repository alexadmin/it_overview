# deployment environment

### production
серверы в ДЦ

### staging
точная копия прода
> A stage or staging environment is an environment for testing that exactly resembles a production environment
>
> -- <cite>https://en.wikipedia.org/wiki/Deployment_environment</cite>

### test 
для работы QA
> The purpose of the test environment is to allow human testers to exercise new and changed code via either automated checks or non-automated techniques.
>
> -- <cite>https://en.wikipedia.org/wiki/Deployment_environment</cite>

### development
десктопы разработчиков


Среды разработки должы быть одинаковы.
Один раз устанавливал Consul, протестировал в тестовой среде, потом поставил в прод. Как потом оказалось на тесте и проде были разные процессоры... Consul написан на Golang, как оказалось определенная версия golang вызывала бесконечный цикл с определенными типом процессоров. Consul начал утилизировать 100% CPU в следствии чего была сильная деградация сервиса.

# networks

Create separate network for each segment. For example if you have rack in DC and DC provider provide LAN to cloud, keep rack servers and cloud VM in different network to avoid loop. If you aimed to configure VLAN from HQ to DC you will get loop with high probability.
