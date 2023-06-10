# React Native Image Uploader
This React Native code allows you to upload images from your app to Imgur and retrieve the image URL. Follow the instructions below to integrate this functionality into your project.

## Prerequisites
Before getting started, make sure you have the following:

* React Native development environment set up
* Imgur API client ID
* Necessary permissions to access the media library on the device

## Installation
  1. Clone or download the project to your local machine.
  2. Install the required dependencies by running the following command in your project directory:
  
   ``npm install`` 
   
   ``npm install expo-image-picker`` 
   
  *You can use another package to choose an image*
  3. Replace the placeholder client ID in the code with your actual Imgur API client ID. Locate the following line
  
  ``Authorization: "Client-ID <YOUR_CLIENT_ID"``,
  
  4. Build and run the project on your desired platform (iOS, Android or Web) using the appropriate commands.

## Usage
To use the image uploader functionality in your app, follow these steps:
  1. Import the necessary components and hooks at the top of your file:

  ``import React, { useState } from 'react';``  </br>
  ``import { TouchableOpacity, Text } from 'react-native';``</br>
  ``import * as ImagePicker from 'expo-image-picker';
``

  2. Define a state variable to store the edited image URL:
  
  ``const [editedImage, setEditedImage] = useState('');``
  
  3. Implement the handleImageUpload function to handle the image upload process:
 ``` 
 const handleImageUpload = async () => {
  const { status } = await ImagePicker.requestMediaLibraryPermissionsAsync();
  if (status !== 'granted') {
    alert('Permission to access the media library is required!');
    return;
  }

  const result = await ImagePicker.launchImageLibraryAsync();
  console.log(result);
  if (!result.cancelled) {
    try {
      const base64Image = await getBase64(result.uri);
      const response = await fetch('https://api.imgur.com/3/image', {
        method: 'POST',
        headers: {
          Authorization: 'Client-ID YOUR_CLIENT_ID',
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          image: base64Image,
        }),
      });

      if (response.ok) {
        const data = await response.json();
        const imageUrl = data.data.link;
        setEditedImage(imageUrl);
        console.log('Image URL:', imageUrl);
      } else {
        console.error('Failed to upload image:', response.status);
      }
    } catch (error) {
      console.error('Error uploading image:', error);
    }
  }
};
```

  4. Implement the getBase64 function to convert the selected image into a base64 format:
```
const getBase64 = async (uri) => {
  const response = await fetch(uri);
  const blob = await response.blob();
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onloadend = () => {
      resolve(reader.result.split(',')[1]);
    };
    reader.onerror = reject;
    reader.readAsDataURL(blob);
  });
};
```

  5. Add the following component to your render function or JSX:
```
<TouchableOpacity onPress={handleImageUpload}>
  <Text
    style={{
      color: appVariables.lightOrangeColor,
      fontWeight: 'bold',
    }}
  >
    Add a Photo
  </Text>
</TouchableOpacity>
```

## Image Display
To display the uploaded image in your React Native app, you can use the Image component from the React Native framework. Add the following code snippet to your file:
```
import { Image } from 'react-native';

// Inside your component...
{editedImage && (
  <Image
    source={{ uri: editedImage }}
    style={{ width: 200, height: 200 }}
  />
)}
```
This code checks if the editedImage state variable has a value and, if so, renders an Image component with the specified width and height, and the uri prop set to the editedImage URL.

## Additional Notes
* Make sure you have installed the required dependencies by running npm install in your project directory.
* Ensure you have replaced the placeholder YOUR_CLIENT_ID in the code with your actual Imgur API client ID.
* For further customization, you can modify the styles of the components to fit your app's design.

## Acknowledgements
- [React Native](https://reactnative.dev/)
- [Imgur API](https://apidocs.imgur.com/)
- [Expo.Dev Docs](https://docs.expo.dev/) </br>
Feel free to modify and enhance this code according to your project's needs. If you encounter any issues or have any questions, please don't hesitate to reach out for assistance.
</br>

*Please note that this project must be tested in a production environment. Running it through localhost may cause errors and is not recommended.*
