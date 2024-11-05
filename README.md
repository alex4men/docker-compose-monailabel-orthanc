# Setting up MONAILabel with Orthanc and OHIF
## Principles
- uses a DICOM image storage (Orthanc) for MONAILabel tasks
  - advantage over file based storage: no conversion needed (i.e. to Niftis), original DICOM files can be used directly without any manipulation/wrangling
- resulting segmentation files are DICOM-SEGs stored in Orthanc

- uses two docker images:
  - Orthanc (holds image and segmentation DICOM data)
  - MONAILabel (used for active learning, model training, pre-segmentations, includes OHIF)

## Installation
### Prerequisites
- ensure correctly configured corporate proxy server
- make sure docker and docker-compose are installed and configured correctly
- CUDA-enabled environment is a must

### Bring up all relevant containers
- clone this repo:
```bash
git clone https://github.com/alex4men/docker-compose-monailabel-orthanc.git
```
- adjust parameters in ./.env first:
  - `$SERVICE_HOST`: machine's hostname, i.e. `hostname.domain.com` or just ip address
  - `$LOCAL_WORKSPACE`: local path to a directory that will contain:
    - `db-index`: Orthanc stores its database index in here (cannot be stored on network mount)
    - `anatomy-model`: MONAILabel stores its model files in here
  - `$IMAGE_MOUNT`: path to a directory where Orthanc stores its images
    - i.e. `/mnt/data/ORTHANC/anatomy-db`
- bring up Orthanc, OHIF and MONAILabel:
```bash
docker compose up --build
```
- Orthanc will be availabel via Web `http://$SERVICE_HOST:8042` or via DICOM QR `$SERVICE_HOST:4242`
  - populate Orthanc with images via Web app or using [batch upload](https://hg.orthanc-server.com/orthanc/file/Orthanc-1.11.1/OrthancServer/Resources/Samples/ImportDicomFiles/ImportDicomFiles.py)
- MONAILabel will be available via `http://$SERVICE_HOST:8000`
- all images on Orthanc can be easily visualized using OHIF `http://$SERVICE_HOST:8000/ohif`
- MONAILabel plugin in 3D Slicer should point to `http://$SERVICE_HOST:8000`
- Also consider to make use of 3D Slicer's Plugin [DICOMWebBrowser] (https://github.com/lassoan/SlicerDICOMwebBrowser), very useful

## Usage
First of all populate Orthanc with DICOM images. Make sure that you import DICOM images that are captured by `$SEARCH_FILTER` of .env file - otherwise MONAILabel will not recognize them.

As an alternative specify paths to an existing Orthanc database: `$IMAGE_MOUNT` should point to Orthanc's data folder and `$LOCAL_WORKSPACE/db-index` should point to Orthanc's index folder.

Use 3D Slicer's MONAILabel plugin to segment images and to train new models.
