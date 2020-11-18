<!doctype html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
        integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
    <title>ESP32 Web Logger</title>
</head>
<style>
.console {
  font-family: 'Courier New';
  width: 90%;
  height: 50%;
  box-sizing: border-box;
  margin: auto;
}

.console header {
  border-top-left-radius: 15px;
  border-top-right-radius: 15px;
  background-color: #202020;
  height: 45px;
  line-height: 45px;
  text-align: center;
  color: #DDD;
}

.console .consolebody {
  border-bottom-left-radius: 15px;
  border-bottom-right-radius: 15px;
  box-sizing: border-box;
  padding: 20px;
  height: 720px;
  background-color: #585858;
  color: #DCDCDC;
}

.console .consolebody p {
  line-height: 1.5rem;
}
</style>
<body>

    <nav class="navbar text-light bg-dark bg-light justify-content-center">
        <span class="navbar-brand mb-0 h1"><center></center>QIBIXX PAYNINO WEB SNIFFER</center></span>
    </nav>
    <br>
    <div class="container justify-content-center">
        <div class="row">
            <div class="col-lg-6 offset-lg-3">
            <form>
                <div class="form-group">
                    <label for="payninoIp"> Paynino IP</label>
                    <input type="text" class="form-control" id="payninoIp">
                  </div>
                  <div class="form-group">
                    <label for="password">Password</label>
                    <input type="password" class="form-control" id="password">
                  </div>
                  <div>
                    <button type="button" class="btn btn-primary" onclick="connectToPaynino()">Connect</button>
                    <button type="button" class="btn btn-danger" onclick="disconnectSock()">Disconnect</button>
                    <div class="custom-control custom-switch">
                        <input type="checkbox" class="custom-control-input" id="customSwitches">
                        <label class="custom-control-label" for="customSwitches">Auto-Scroll</label>
                    </div>
                    
                </div>
            </form>
        </div>
    </div>
</div>
        <br>   

    <div class="row">
        <div class="col-sm">
            <div class="console">
                <header>
                  <p>Output</p>
                </header>
                <div class="consolebody overflow-auto" id="console">
                </div>
              </div>
        </div>
    </div>

    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js"
        integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo"
        crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js"
        integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1"
        crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js"
        integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM"
        crossorigin="anonymous"></script>
</body>

<script>
    let loginPassword;
    let payninoIp 
    let socket;

    function connectToPaynino() {
        console.log("Connect!")
        loginPassword = document.getElementById("password").value;
        payninoIp = document.getElementById("payninoIp").value;
        openSocket();
    }

    function disconnectSock(){
        socket.close();
    }

    function openSocket() {
        let wsEndpoint = "ws://".concat(payninoIp).concat("/ws")
        socket = new WebSocket(wsEndpoint);

        socket.onopen = function (e) {
            console.log("Socket Open. Sending psw");
            socket.send(loginPassword);

        };

        socket.onmessage = function (event) {
            let newMsg = event.data;
            //   console.log(`[SockRCV] ${newMsg}`);
            let node = document.createElement("p");
            let textNode = document.createTextNode(newMsg);
            node.appendChild(textNode);
            document.getElementById("console").appendChild(node);

            if (document.getElementById("customSwitches").checked){
                let elem = document.getElementById('console');
                elem.scrollTop = elem.scrollHeight;
            }
            console.log("Scroll: ",document.getElementById("customSwitches").value);
            
        };

        socket.onclose = function (event) {
            if (event.wasClean) {
                // alert(`[close] Connection closed cleanly, code=${event.code} reason=${event.reason}`);
                alert("Connection Closed");
            } else {
                alert('Connection Lost');
            }
        };

        socket.onerror = function (error) {
            alert(`[error] ${error.message}`);
        };
    }

</script>
</html>