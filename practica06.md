# Práctica 6

## ClienteAvatarInfo

Información específica sobre la imagen de avatar del cliente. La clase siguiente incorpora más información y comportamiento para admitir también el uso de imágenes basadas en [Gravatar](https://es.wikipedia.org/wiki/Gravatar). Puede usarse esta clase u otra escrita solamente con la información necesaria para cumplir con el caso de uso pedido.

```java
package es.unirioja.paw.web.data;

import es.unirioja.paw.jpa.ClienteEntity;

public class ClienteAvatarInfo {

    private String gravatarHashId;
    private String clienteImage;
    private ImageSource imageSource;

    private enum ImageSource {
        Default,
        Gravatar,
        LocalImage
    }

    public ClienteAvatarInfo(ClienteEntity cliente) {
        imageSource = ImageSource.Default;
        this.gravatarHashId = cliente.getGravatarHashId();
        this.clienteImage = cliente.getImagenAvatar();
        if (gravatarHashId != null && !gravatarHashId.isBlank()) {
            imageSource = ImageSource.Gravatar;
        }
        // La imagen subida tiene preferencia sobre Gravatar
        if (clienteImage != null && !clienteImage.isBlank()) {
            imageSource = ImageSource.LocalImage;
        }

    }

    public boolean isDefault() {
        if (imageSource == null) {
            return true;
        }
        if (imageSource == ImageSource.Default) {
            return true;
        }
        return false;
    }

    public boolean isRemoteURL() {
        if (imageSource == null) {
            return false;
        }
        switch (imageSource) {
            case Gravatar:
                return true;
        }
        return false;
    }

    public String getImageSource() {
        if (imageSource == null) {
            return null;
        }
        switch (imageSource) {
            case LocalImage:
                return clienteImage;
            case Gravatar:
                return String.format("https://gravatar.com/avatar/%s", gravatarHashId);
        }

        return null;
    }

}
```

## FileSystemStorageService

En la sesión 3 se hace referencia a FileStorageService. Se muestra a continuación una posible implementación de esta interfaz.

```java
package es.unirioja.paw.service;

import es.unirioja.paw.exception.StorageException;
import java.io.IOException;
import java.io.InputStream;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.nio.file.StandardCopyOption;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Service;
import org.springframework.web.multipart.MultipartFile;

@Service
public class FileSystemStorageService implements FileStorageService {

    private final Logger logger = LoggerFactory.getLogger(this.getClass());

    //
    // NOTA: esta clase implementa la interfaz FileStorageService. 
    // Piensa en cómo se acopla a un tipo de almacenamiento concreto
    // y a entidades del framework Spring
    //
    /**
     * @param file El fichero subido
     * @param path Ruta destino
     */
    @Override
    public void store(MultipartFile file, String path) {
        final Path targetPath = Paths.get(path);
        try {
            if (file.isEmpty()) {
                throw new StorageException("No se puede guardar un fichero vacío");
            }
            Path destinationFile = targetPath.resolve(
                    Paths.get(file.getOriginalFilename()))
                    .normalize().toAbsolutePath();
            if (!destinationFile.getParent().equals(targetPath.toAbsolutePath())) {
                // Peligro de ataque Graph Traversal 
                throw new StorageException(
                        "No se puede guardar un fichero fuera del directorio indicado");
            }
            logger.info("Destination file: {}", destinationFile);
            try (InputStream inputStream = file.getInputStream()) {
                Files.copy(inputStream, destinationFile,
                        StandardCopyOption.REPLACE_EXISTING);
            }
        } catch (IOException ex) {
            throw new StorageException("Error al guardar", ex);
        }
        logger.info("Fichero guardado: {} ({})", file.getOriginalFilename(), path);
    }

}
```