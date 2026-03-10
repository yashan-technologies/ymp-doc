The main ports required during the YMP operation process are as follows.

## Default Built-in Database Installation
|Port |Port Number |Description |Connection and Authentication |Encryption Method |
|----------|------|-----------------------|----------------------------------------------------------------------------|---------------|
| YMP Service Listen  | 8090         | For providing YMP services externally.     | Based on JWT authentication HTTP protocol                                                                            | No encryption        |
| Built-in Database Listen | 8091         | For providing database services externally. | <ul><li> Remote connection: Based on AEC+SHA256 password authentication TCP long connection</li><li> UDS local connection: Based on operating system authentication IPC connection</li></ul>| Supports SSL encryption, default not encrypted |
| yasom       | 8093         | Internal communication port for receiving and responding to yasboot commands. | Based on AEC+SHA256 password authentication TCP short connection                                                     | Supports SSL encryption, default not encrypted |
| yasagent    | 8094         | Internal communication port for receiving and responding to yasom instructions. | Based on AEC+SHA256 password authentication TCP short connection                                                     | Supports SSL encryption, default not encrypted |

## Custom Built-in Database Installation
|Port |Port Number |Description |Connection and Authentication |Encryption Method |
|----------|------|-----------------------|-------|------|
| YMP Service Listen  | 8090         | For providing YMP services externally.     | Based on JWT authentication HTTP protocol   | No encryption        |