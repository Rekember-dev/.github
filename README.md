# Rekember

Rekember is a custom Ragnarok Online project built on rAthena.  
This organization contains the core server, client, tooling, and deployment repositories that power the project.

---

## Repositories

### rekember-server  
Core game server (rAthena-based).  
All gameplay logic, custom skills, systems, and database changes live here.  
https://github.com/Rekember-dev/rekember-server

---

### rekember-client  
Client-side assets and configuration.  
Includes GRFs, sprites, UI changes, and client data files required to run Rekember.  
https://github.com/Rekember-dev/rekember-client

---

### rekember-parser  
Item text parser tool created by Mizu.  
Used to safely edit and modify Ragnarok item text files without corrupting formatting that can occur in standard text editors.  
https://github.com/Rekember-dev/rekember-parser

---

### rekember-deploy  
Production deployment repository.  
Contains live-ready builds and deployment configuration for the Rekember server.  
https://github.com/Rekember-dev/rekember-deploy

---

## Project Structure Overview

- `rekember-server` → Game logic and systems  
- `rekember-client` → Game assets and client data  
- `rekember-parser` → Tooling support  
- `rekember-deploy` → Live deployment pipeline  

---

Rekember is an actively developed project.  
More documentation will be added as systems stabilize.
