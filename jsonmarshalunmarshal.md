## Marshal and unmarshal json.RawMessage struct example

Golang's json.RawMessage type is a good way to deal with generic JSON objects other than interface{}.    
There are times when JSON objects have different type of structs but with common identifier such as ID tag, name or type.    
In this example, we will learn how to use json.RawMessage type to handle different struct in the input JSON objects    
and use switch statement to handle the different types accordingly.

```golang
 package main

 import (
 	"encoding/json"
 	"fmt"
 )

 type Message struct {
 	Type      string `json:type`
 	Timestamp string `json:timestamp`
 	Data      json.RawMessage
 }

 type Event struct {
 	Type    string `json:type`
 	Creator string `json:creator`
 }

 type News struct {
 	Type    string `json:type`
 	Creator string `json:creator`
 }

 var JSONEventData = []byte(`{
   "type": "event",
   "data": {
        "type": "outdoor",
        "creator": "adam"
   }}`)

 var JSONNewsData = []byte(`{
   "type": "news",
   "data": {
        "type": "indoor",
        "creator": "eve"
   }}`)

 func main() {

 	var m Message

 	// unmarshal JSONEventData to message struct
 	if err := json.Unmarshal(JSONEventData, &m); err != nil {
 		fmt.Println(err)
 	}	

     // uncomment these 3 lines to test different type with JSONNewsData instead of EventData
 	//if err := json.Unmarshal(JSONNewsData, &m); err != nil {
 	//	fmt.Println(err)
 	//}	


 	switch m.Type {
 	case "event":
 		var e Event
 		if err := json.Unmarshal([]byte(m.Data), &e); err != nil {
 			fmt.Println(err)
 		}
 		fmt.Printf("JSON type is [%v] and it is [%v] event created by [%v] \n", m.Type, e.Type, e.Creator)

 	case "news":
 		var n News
 		if err := json.Unmarshal([]byte(m.Data), &n); err != nil {
 			fmt.Println(err)
 		}
 		fmt.Printf("JSON type is [%v] and it is [%v] event created by [%v] \n", m.Type, n.Type, n.Creator)

 	default:
 		fmt.Println("unable to unmarshal JSON data or differentiate the type")
 	}
 }
 ```
 
Sample output :
```
JSON type is [event] and it is [outdoor] event created by [adam]
If you are looking for a simpler example on how to marshal and unmarshal json.RawMessage. Here it is :
```

```golang
 package main

 import (
 	"encoding/json"
 	"fmt"
 )

 type Employee struct {
 	Id         int
 	Name       string
 	RawMessage *json.RawMessage
 }

 type Secret struct {
 	Message string
 }

 func main() {
 	sec := Secret{"I love you!"}

 	byte1, err := json.Marshal(sec)

 	if err != nil {
 		fmt.Println(err)
 	}

 	raw := json.RawMessage(byte1)
 	fmt.Printf("Raw Message : %s\n", raw)

 	emp := Employee{0, "Adam", &raw}

 	byte2, err := json.Marshal(emp)

 	if err != nil {
 		fmt.Println(err)
 	}

 	fmt.Printf("Employee's secret message : %s\n", string(byte2))

 	// -----------------------
 	// unmarshal json.RawMessage

 	fmt.Println("------------------------------------")

 	var e Employee
 	err = json.Unmarshal(byte2, &e)
 	if err != nil {
 		fmt.Println(err)
 	}
 	fmt.Printf("emp.Json : %s \n", string(*emp.RawMessage))

 	var sec2 Secret
 	err = json.Unmarshal([]byte(*e.RawMessage), &sec2)
 	if err != nil {
 		fmt.Println(err)
 	}
 	fmt.Printf("Secret Message : %s \n", sec2)

 }
 ```
 
Output :

```
Raw Message : {"Message":"I love you!"}
Employee's secret message : {"Id":0,"Name":"Adam","RawMessage":{"Message":"I love you!"}}
emp.Json : {"Message":"I love you!"}
Secret Message : {I love you!}
```
