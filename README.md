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
