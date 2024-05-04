# Software Development Cycle

## 1. Requirements Gathering

- Understand the needs and expectations of stakeholders.
- Document functional and non-functional requirements.

## 2. Planning

- Define project scope, timeline, and resources.
- Create a development roadmap.

## 3. Design

- Architectural design: Define system components and their interactions.
- Detailed design: Design data structures, algorithms, and user interfaces.

## 4. Implementation

- Write code according to design specifications.
- Regularly test and debug to ensure correctness.

## 5. Testing

- Unit testing: Test individual components.
- Integration testing: Test interactions between components.
- System testing: Test the entire system.

## 6. Deployment

- Deploy the software in the production environment.
- Configure servers and networks as needed.

## 7. Maintenance

- Monitor software performance and user feedback.
- Fix bugs and make improvements as necessary.

---

# File Permissions in Unix/Linux: `chown` and `chmod`

- `chown`: Changes the owner and group of a file or directory.

```

chown user:group filename

```

- `chmod`: Changes the permissions of a file or directory.

```

chmod permissions filename

```

Example:

```

chown user1:group1 file.txt
chmod 755 file.txt

```

In this example:

- `chown user1:group1 file.txt` changes the owner of `file.txt` to `user1` and the group to `group1`.
- `chmod 755 file.txt` sets the permissions of `file.txt` to read, write, and execute for the owner, and read and execute for others.

---

# UDP (User Datagram Protocol) vs TCP (Transmission Control Protocol)

## UDP:

- **Application Example**: DNS (Domain Name System)
  - DNS uses UDP for name resolution queries. When your browser needs to resolve a domain name (like www.example.com) to an IP address, it sends a UDP packet to a DNS server requesting the IP address associated with the domain.

## TCP:

- **Application Example**: HTTP (Hypertext Transfer Protocol)
  - HTTP, the protocol used for web browsing, relies on TCP for reliable data transmission. When you visit a website, your browser establishes a TCP connection with the web server to exchange HTTP messages containing requests and responses for web pages.

These examples illustrate how UDP and TCP are used in real-world applications to facilitate communication between clients and servers, with UDP prioritizing speed and TCP prioritizing reliability.

---

# Commands in vi or vim Text Editor

- `i`: Enter insert mode to start typing text.
- `Esc`: Exit insert mode and return to command mode.
- `:w`: Save changes to the file.
- `:q`: Quit the editor.
- `:wq`: Save changes and quit.
- `dd`: Delete the current line.
- `yy`: Copy the current line.
- `p`: Paste the copied line.
