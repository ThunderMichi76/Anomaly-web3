# clona tu repo (o entra al repo local si ya lo tienes)
git clone https://github.com/ThunderMichi76/Anomaly-web3.git
cd Anomaly-web3

# actualizar la rama principal
git checkout main
git pull origin main

# eliminar el archivo y commitear
git rm README.md
git commit -m "chore: remove README.md"
git push origin main
