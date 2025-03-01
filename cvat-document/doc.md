#1️⃣ Save Each Image as a TAR File
docker save -o cvat_ui.tar cvat/ui:dev
docker save -o cvat_server.tar cvat/server:dev
docker save -o postgres.tar postgres:15-alpine
docker save -o opa.tar openpolicyagent/opa:0.63.0
docker save -o clickhouse.tar clickhouse/clickhouse-server:23.11-alpine
docker save -o traefik.tar traefik:v2.10
docker save -o kvrocks.tar apache/kvrocks:2.7.0
docker save -o redis.tar redis:7.2.3-alpine
docker save -o grafana.tar grafana/grafana-oss:10.1.2
docker save -o vector.tar timberio/vector:0.26.0-alpine

ls -lh *.tar
3️⃣ Load the Images on Another Machine

docker load -i cvat_ui.tar
docker load -i cvat_server.tar
docker load -i postgres.tar
docker load -i opa.tar
docker load -i clickhouse.tar
docker load -i traefik.tar
docker load -i kvrocks.tar
docker load -i redis.tar
docker load -i grafana.tar
docker load -i vector.tar

for file in *.tar; do docker load -i "$file"; done

1️⃣ Stop All Running Containers
docker stop $(docker ps -aq)

2️⃣ Remove All Containers
docker rm $(docker ps -aq)

3️⃣ Remove All Images
docker rmi -f $(docker images -aq)

4️⃣ Remove All Volumes
docker volume rm $(docker volume ls -q)

5️⃣ Remove All Networks (except default ones)
docker network rm $(docker network ls -q)

6️⃣ Remove Everything in One Command
docker system prune -a --volumes

docker images
docker ps



#cvat main commands

General Shortcuts
F1 → Show help menu (list of shortcuts)
Ctrl + S → Save annotations
Ctrl + Z → Undo last action
Ctrl + Shift + Z → Redo last undone action
Ctrl + C → Copy selected annotation
Ctrl + V → Paste annotation
Delete → Delete selected annotation

Navigation & View
Space → Play/Pause video
← (Left Arrow) → Previous frame
→ (Right Arrow) → Next frame
Shift + ← → Skip 10 frames backward
Shift + → → Skip 10 frames forward
Alt + Mouse Scroll → Zoom in/out
H → Hide/Show annotations

Annotation Tools
N → Create a new object (bounding box, polygon, etc.)
B → Bounding box mode
P → Polygon mode
S → Skeleton mode
T → Tagging mode
Ctrl + D → Duplicate annotation
Esc → Cancel current annotation action
Shift + Left Click → Add point to polygon
Ctrl + Click → Move polygon points

Object Tracking & Labels
L → Lock/Unlock an object
Ctrl + Left Click → Select multiple objects
Ctrl + Shift + Left Click → Select all instances of an object
Ctrl + G → Group selected objects
Ctrl + Shift + G → Ungroup objects

Interpolation & Keyframes
I → Interpolation mode
K → Add/remove keyframe
Ctrl + K → Remove keyframe
M → Merge annotations

Advanced Tools
Alt + Drag → Move annotation
Shift + Drag → Resize annotation
F → Focus on selected object
D → Deselect object
E → Edit mode

Exporting & Importing
Ctrl + Shift + E → Export annotations
Ctrl + Shift + I → Import annotations





#🔹 Exporting Annotations from CVAT (COCO Format)

1.Create & Annotate a Task:
Create a new task in CVAT.
Upload images and annotate them.

2.Export Annotations:
Open the task/job in CVAT.
Click on "Export Annotations" (top-right corner).
Select "COCO 1.0" as the export format.
Download the ZIP file after processing.

3.Extract the ZIP File:
unzip annotations.zip -d coco_annotations
cd coco_annotations

You will see:
annotations/
  ├── instances_default.json
images/
  ├── your_images_here.png
  


#🔹 Importing COCO Annotations into CVAT

1. Create a New Task in CVAT
Click "Create Task" and upload the same images used in the exported dataset.
Define the same class names as in the exported dataset.

2. Upload the Annotations
Open the newly created task.
Locate "Jobs" (below) and click the three dots (...) on the right.
Select "Import annotations"
Choose eg:- COCO 1.0 as the import format.
Upload instances_default.json from the extracted dataset folder.

3. Confirm Import
CVAT will display a warning:

"Current annotation will be lost. You are going to upload new annotations to job #X. Continue?"

Click "Yes" to proceed.
Your annotations should now be successfully imported and visible! ✅








#cvat 
CVAT for offline automatic annotation, the best approach is to use self-hosted models deployed with Nuclio 

#steps
cd ~/DevOps_Learning/25_CVAT/cvat
docker compose -f docker-compose.yml -f docker-compose.dev.yml -f components/serverless/docker-compose.serverless.yml up -d --build
docker compose ps
http://localhost:8070



# Nuclio’s nuctl CLI tool 
# Download Nuclio CLI (replace <version> with 1.13.0)
wget https://github.com/nuclio/nuclio/releases/download/1.13.0/nuctl-1.13.0-linux-amd64

# Give execution permissions
sudo chmod +x nuctl-1.13.0-linux-amd64

# Create a symbolic link so it can be used globally
sudo ln -sf $(pwd)/nuctl-1.13.0-linux-amd64 /usr/local/bin/nuctl

# Verify installation
nuctl version




########################
./cvat/serverless/deploy_cpu.sh cvat/serverless/openvino/omz/public/yolo-v3-tf

#verfiy model is deploy or not into a cvat
http://localhost:8070
http://localhost:8080








#####
you should be able to deploy and use the YOLOv3-TF model for automatic annotation in CVAT.
nuctl get functions


✅ Restart CVAT if needed:
docker compose -f docker-compose.yml -f components/serverless/docker-compose.serverless.yml down
docker compose -f docker-compose.yml -f components/serverless/docker-compose.serverless.yml up -d


✅ Test auto annotation:
Go to CVAT UI (http://localhost:8080)

