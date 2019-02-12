## Xamarin - získání fotek přes DependencyService

Ronald Rebernigg

V tomto článku je popsáno získání obrázků z galerie Androidu pomoci DependencyInjection a poslání je do sdílené části projektu v Xamarinu.

### Ukázka

#### Třída ImageInfo

Tato třída reprezentuje obrázek, včetně Base64 kódu obrázku.

**ImageInfo.cs**

<code class="language-C# ">public class ImageInfo
    {
        public string ImageName { get; set; }
        public string ImagePath { get; set; }
        public string Base64Code { get; set; }
    }</code>

#### Získání obrázků v Androidu

Všechny obrázky jsou převedeny do Base64 kódu a vloženy do kolekce, vše v projektu pro Android. Poté jsou v sdílené části získány pomoci DependencyInjection a je s nimi možné dále pracovat.

**Page v Android projektu**

<code class="language-C# ">//DependencyInjection
    [assembly: Dependency(typeof(LoadImage))]
    namespace BlackPhoto.Droid
    {
        //Implementace interfacu ILoadImage
        public class LoadImage : ILoadImage
        {
            public List<imageinfo> GetImages()
            {
                //Inicializace Listu, do kterého se ukládají jednotlivé instance třídy ImageInfo
                List<imageinfo> classList = new List<imageinfo>();

                //Složka na zařízení Camera
                File ExtCameraDirectory = new File(Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDcim), "Camera");

                //Složka na zařízení 100ANDRO
                File Ext100ANDRODirectory = new File(Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDcim), "100ANDRO");

                //Provede kód pokud je složka 100ANDRO prázdná
                if (Ext100ANDRODirectory.ListFiles().Length == 0)
                {
                    File[] files = ExtCameraDirectory.ListFiles();

                    foreach (File file in files)
                    {
                        //Ověření zdali se jedná o soubor ne o složku
                        if (file.IsFile)
                        {
                            //Ověření zdali se jedná o soubor jpg nebo png
                            if (file.Name.EndsWith(".jpg") || file.Name.EndsWith(".png"))
                            {
                                EncodeImages(ExtCameraDirectory, file, classList);

                            }
                        }
                    }
                }
                else
                {
                    File[] files = Ext100ANDRODirectory.ListFiles();
                    foreach (File file in files)
                    {
                        //Ověření zdali se jedná o soubor ne o složku
                        if (file.IsFile)
                        {
                            //Ověření zdali se jedná o soubor jpg nebo png
                            if (file.Name.EndsWith(".jpg") || file.Name.EndsWith(".png"))
                            {
                                EncodeImages(Ext100ANDRODirectory, file, classList);
                            }
                        }
                    }
                }

                //Vrátí kolekci objektů ImageInfo
                return classList;
            }

            public void EncodeImages(File directory, File image, List<imageinfo> imageList)
            {
                //Převedení obrázku na Base64
                Bitmap imgBitmap = BitmapFactory.DecodeFile(directory + "/" + image.Name);
                MemoryStream ms = new MemoryStream();
                imgBitmap.Compress(Bitmap.CompressFormat.Jpeg, 20, ms);
                byte[] imgByte = ms.ToArray();
                String imgString = Base64.EncodeToString(imgByte, Base64Flags.Default);

                //Přidání objektu ImageInfo do kolekce
                imageList.Add(new ImageInfo() { ImageName = image.Name, ImagePath = image.AbsolutePath, Base64Code = imgString });

                //Vyčištění MemoryStreamu
                ms.Dispose();
            }
        }
    }</imageinfo></imageinfo></imageinfo></imageinfo></code>

#### Získání obrázků ve sdílené části

Získá obrázky pomoci **DependencyService.Get().GetImages()**, které jsou zpracovány metodou **ImgFromBase64**. Obrázky jsou vloženy do složky Images v adresáři aplikace. Obrázek je zpátky z Base64 kódu převeden pomoci Streamu a je s ním možné dále pracovat, například zobrazit do ListView, kdy se Source elementu Image získá takto **ImageSource.FromStream((() => ImageStream))**

**Sdílená část projektu**

<code class="language-C# ">public MainPage()
        {
            InitializeComponent();
            ImgFromBase64(DependencyService.Get<iloadimage>().GetImages());
        }
            public async void ImgFromBase64(List<imageinfo> image)
        {

        foreach (ImageInfo x in image)
        {
            //Ověření jestli složka existuje
            bool imagesExists = await FolderExists("Images");

            //Pokud je vytvořena složka, tak pracuje se soubory
            if (imagesExists)
            {
                //Vytvoření instance IFolder pro složku Images
                IFolder rootFolder = FileSystem.Current.LocalStorage;
                IFolder scanned = await rootFolder.GetFolderAsync("Images");

                //Převedení Base64 kódu zpět na obrázek
                IFile imgFile = await scanned.CreateFileAsync(x.ImageName, CreationCollisionOption.ReplaceExisting);
                byte[] imageBytes = Convert.FromBase64String(x.Base64Code);
                Stream s = await imgFile.OpenAsync(FileAccess.ReadAndWrite);
                s.Write(imageBytes, 0, imageBytes.Length);
                s.Dispose();

                //Displaying images from local directory
                IFile imgFileLoad = await scanned.GetFileAsync(x.ImageName);
                Stream loadStream = await imgFileLoad.OpenAsync(FileAccess.Read);

            }
            else
            {
                //Pokud není v lokálním uložišti vytvořena složka Images, tak ji vytvoří
                IFolder folder = FileSystem.Current.LocalStorage;
                folder = await folder.CreateFolderAsync("Images", CreationCollisionOption.ReplaceExisting);
            }
        }
    }</imageinfo></iloadimage></code>