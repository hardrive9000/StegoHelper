## Summary
Simple helper to hide text into an image using LSB steganography. It also supports AES-256 encryption to enforce security of hidden text.

## Example
```csharp
using Cocona;
using StegoHelper;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.PixelFormats;

namespace PoCSteganography
{
    class Program
    {
        static void Main(string[] args)
        {
            CoconaLiteApp.Run<Program>(args);
        }

        public void Hide([Option('f')] string imageFile, [Option('t')] string textFile, [Option('p')] string password = "")
        {
            if (File.Exists(imageFile) && File.Exists(textFile))
            {
                using (Image<Rgba32> inputImage = Image.Load<Rgba32>(imageFile))
                {
                    using (StreamReader sr = File.OpenText(textFile))
                    {
                        string text = sr.ReadToEnd();

                        if (!string.IsNullOrEmpty(password))
                            text = AESEncryptionHelper.Encrypt(text, password);

                        if (inputImage.Height * inputImage.Width * 3 / 8 >= text.Length)
                        {
                            Image outImage = SteganographyHelper.EmbedText(text, inputImage);
                            outImage.SaveAsPng("hidden.png");
                        }
                        else
                            Console.WriteLine("Espacio insuficente para almacenar el texto. Se requiere una imagen de mayores dimensiones");
                    }
                }
            }
            else
                Console.WriteLine("Archivo no encontrado");
        }

        public void Unhide([Option('f')] string file, [Option('p')] string password = "")
        {
            if (File.Exists(file))
            {
                using (Image<Rgba32> inputImage = Image.Load<Rgba32>(file))
                {
                    string text = SteganographyHelper.ExtractText(inputImage);

                    if (!string.IsNullOrEmpty(password))
                        text = AESEncryptionHelper.Decrypt(text, password);

                    File.WriteAllText("secret.txt", text);
                }
            }
            else
                Console.WriteLine("Archivo no encontrado");
        }
    }
}
```
## Videotutorial - C# Steganography Step by Step (Spanish)
[![](https://img.youtube.com/vi/-mU5D37Istw/0.jpg)](https://www.youtube.com/watch?v=-mU5D37Istw)
