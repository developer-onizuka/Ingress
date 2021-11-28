# Ingress

# 1. git Clone
```
git clone https://github.com/developer-onizuka/Ingress
cd Ingress
```

# 2. Nginx Ingress Controller
- https://kubernetes.github.io/ingress-nginx/deploy/#quick-start
```
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```
```
# kubectl get ingressclass
NAME    CONTROLLER             PARAMETERS   AGE
nginx   k8s.io/ingress-nginx   <none>       5m16s
```
If you wanna uninstall, then you might use the command below:
```
helm delete $(helm ls -n ingress-nginx | awk '/ingress-nginx/{print $1}') -n ingress-nginx
kubectl delete ns ingress-nginx
```

# 3. Service of Cat and Dog
```
kubectl apply -f cat/nginx_cat.yaml
kubectl apply -f dog/nginx_dog.yaml
```

# 4. Check Ingress created for each service
```
# kubectl get ingress
NAME   CLASS   HOSTS                 ADDRESS           PORTS   AGE
cat    nginx   animals.example.com   192.168.121.224   80      70s
dog    nginx   animals.example.com   192.168.121.224   80      57s
```

# 5. Edit /etc/hosts
```
# echo "192.168.121.224 animals.example.com" >> /etc/hosts
```

# 6. Access thru Browser
```
# curl animals.example.com/cat/index.html
cat
<pre>
          | \\\                 /__\
         |.\\\ \               //--.i
         i \\\\ \             ///// i
         i \\\\\ \  _______  //////:|
         i.\\\\\\ --|i | i|-- /////.|
         |:\\\\  |||:i i iiii|  /// i
         \ \     / i|i | |||i\      i
         /       :| || i i| i       i
         |       |:  i | | |i|      \
         i        | || | i  i|       i
        |      /     | : |     \\    i
        |     //  __  :.:: __   \    i
        |        / _\     / _\       i
        |       | # \      # \       i
        i  ___--| \_)      \_)--___  |
        i====    \__/     \__/   ====|
        i==                        ==|
        i-            _-_           -i
       /|             \ /            i
     //  \    _ _ ---  | -- _       /
    /    _\- -   _--   |  --_ --_  /
   /    -  \_ _-  _-   |  --  -_  /_
    /     _ -\_ -- -__/ \__- -_ _/_ -
 //      -   _---__  \___/ --__/_  -
           _-                    -_
/
</pre>
```
```
# curl animals.example.com/dog/index.html
dog
<pre>
          ____
       ,-'-,  `---._
_______(0} `, , ` , )
V           ; ` , ` (                            ,'~~~~~~`,
`.____,- '  (,  `  , )                          :`,-'""`. ";
  `-------._);  ,  ` `,                         \;:      )``:
         )  ) ; ` ,,  :                          ``      : ';
        (  (`;:  ; ` ;:\                                 ;;;,
        (:  )``;:;;)`'`'`--.    _____     ____       _,-';;`
        :`  )`;)`)`'   :    "~~~     ~~~~~    ~~~`--',.;;;'
        `--;~~~~~      `  ,  ", """,  "  "   "` ",, \ ;``
          ( ;         ,   `                ;      `; ;
          (; ; ;      `                   ,`       ` :
           (; /            ;   ;          ` ;     ; :
           ;(_; ;  :   ; ; `; ;` ; ; ,,,""";}     `;
           : `; `; `  :  `  `,,;,''''   );;`);     ;
           ;' :;   ; : ``'`'           (;` :( ; ,  ;
           |, `;; ,``                  `)`; `(; `  `;
           ;  ;;  ``:                   `).:` \;,   `.
        ,-'   ;`;;:;`                   ;;'`;;  `)   )
         ~~~,-`;`;,"                    ~~~~~  ,-'   ;
            """"""                             `"""""
</pre>
```

# 7. Istio and Ingress

- Before istio-injection enabled
```
# kubectl get pods -o wide
NAME                             READY   STATUS    RESTARTS   AGE    IP              NODE      NOMINATED NODE   READINESS GATES
nginx-cat-test-cd76c496-c58jx    1/1     Running   0          109m   10.10.235.151   worker1   <none>           <none>
nginx-cat-test-cd76c496-q4sb9    1/1     Running   0          109m   10.10.235.148   worker1   <none>           <none>
nginx-dog-test-867696956-vrhmw   1/1     Running   0          109m   10.10.235.155   worker1   <none>           <none>
nginx-dog-test-867696956-vszg5   1/1     Running   0          109m   10.10.235.152   worker1   <none>           <none>
```

```
# kubectl label namespaces default istio-injection=enabled
namespace/default labeled

# kubectl delete pods --all
pod "nginx-cat-test-cd76c496-c58jx" deleted
pod "nginx-cat-test-cd76c496-q4sb9" deleted
pod "nginx-dog-test-867696956-vrhmw" deleted
pod "nginx-dog-test-867696956-vszg5" deleted

# kubectl get pods -o wide
NAME                             READY   STATUS    RESTARTS   AGE   IP              NODE      NOMINATED NODE   READINESS GATES
nginx-cat-test-cd76c496-c9nz7    2/2     Running   0          32s   10.10.235.154   worker1   <none>           <none>
nginx-cat-test-cd76c496-p97p5    2/2     Running   0          32s   10.10.235.182   worker1   <none>           <none>
nginx-dog-test-867696956-68w7s   2/2     Running   0          32s   10.10.215.24    worker9   <none>           <none>
nginx-dog-test-867696956-w9h6f   2/2     Running   0          32s   10.10.215.28    worker9   <none>           <none>
```
```
# curl animals.example.com/cat/index.html
cat
<pre>
          | \\\                 /__\
         |.\\\ \               //--.i
         i \\\\ \             ///// i
         i \\\\\ \  _______  //////:|
         i.\\\\\\ --|i | i|-- /////.|
         |:\\\\  |||:i i iiii|  /// i
         \ \     / i|i | |||i\      i
         /       :| || i i| i       i
         |       |:  i | | |i|      \
         i        | || | i  i|       i
        |      /     | : |     \\    i
        |     //  __  :.:: __   \    i
        |        / _\     / _\       i
        |       | # \      # \       i
        i  ___--| \_)      \_)--___  |
        i====    \__/     \__/   ====|
        i==                        ==|
        i-            _-_           -i
       /|             \ /            i
     //  \    _ _ ---  | -- _       /
    /    _\- -   _--   |  --_ --_  /
   /    -  \_ _-  _-   |  --  -_  /_
    /     _ -\_ -- -__/ \__- -_ _/_ -
 //      -   _---__  \___/ --__/_  -
           _-                    -_
/
</pre>
```
```
# curl animals.example.com/dog/index.html
dog
<pre>
          ____
       ,-'-,  `---._
_______(0} `, , ` , )
V           ; ` , ` (                            ,'~~~~~~`,
`.____,- '  (,  `  , )                          :`,-'""`. ";
  `-------._);  ,  ` `,                         \;:      )``:
         )  ) ; ` ,,  :                          ``      : ';
        (  (`;:  ; ` ;:\                                 ;;;,
        (:  )``;:;;)`'`'`--.    _____     ____       _,-';;`
        :`  )`;)`)`'   :    "~~~     ~~~~~    ~~~`--',.;;;'
        `--;~~~~~      `  ,  ", """,  "  "   "` ",, \ ;``
          ( ;         ,   `                ;      `; ;
          (; ; ;      `                   ,`       ` :
           (; /            ;   ;          ` ;     ; :
           ;(_; ;  :   ; ; `; ;` ; ; ,,,""";}     `;
           : `; `; `  :  `  `,,;,''''   );;`);     ;
           ;' :;   ; : ``'`'           (;` :( ; ,  ;
           |, `;; ,``                  `)`; `(; `  `;
           ;  ;;  ``:                   `).:` \;,   `.
        ,-'   ;`;;:;`                   ;;'`;;  `)   )
         ~~~,-`;`;,"                    ~~~~~  ,-'   ;
            """"""                             `"""""
</pre>
```

# 8. Access Kiali thru Browser
Expose kiali service to outside using Type LoadBalancer. You might use "edit" as below:
```
# kubectl edit service kiali -n istio-system
```

So you can find Kiali exposed by LoadBalancer to outside.
```
# kubectl get services -n istio-system
NAME                   TYPE           CLUSTER-IP       EXTERNAL-IP       PORT(S)                                      AGE
grafana                ClusterIP      10.104.160.57    <none>            3000/TCP                                     3d23h
istio-ingressgateway   LoadBalancer   10.109.196.134   192.168.121.220   15021:32009/TCP,80:31573/TCP,443:30508/TCP   3d23h
istiod                 ClusterIP      10.106.237.193   <none>            15010/TCP,15012/TCP,443/TCP,15014/TCP        3d23h
jaeger-collector       ClusterIP      10.107.120.18    <none>            14268/TCP,14250/TCP,9411/TCP                 3d23h
kiali                  LoadBalancer   10.104.102.182   192.168.121.222   20001:31219/TCP,9090:31276/TCP               3d23h
prometheus             ClusterIP      10.100.172.120   <none>            9090/TCP                                     3d23h
tracing                ClusterIP      10.108.229.244   <none>            80/TCP,16685/TCP                             3d23h
zipkin                 ClusterIP      10.97.40.27      <none>            9411/TCP                                     3d23h
```
