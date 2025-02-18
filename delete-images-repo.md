To delete all Docker images from a repository with the name pattern `reg.example.com:*`, you can use the following command:

### **Steps:**
1. **List all images from the repository**
   ```sh
   docker images | grep "reg.example.com:" 
   ```
   This will show all images that match the repository pattern.

2. **Delete all matching images**
   Run the following command to remove them:
   ```sh
   docker images | grep "reg.example.com:" | awk '{print $3}' | xargs docker rmi -f
   ```

### **Explanation:**
- `docker images` → Lists all images.
- `grep "reg.example.com:"` → Filters images with the repository name `reg.example.com:`.
- `awk '{print $3}'` → Extracts the image ID.
- `xargs docker rmi -f` → Deletes the images forcefully.

If you're using a private Docker registry (`reg.example.com`), you may need to delete images directly from the registry using the registry API.

Let me know if you need further assistance! 🚀
