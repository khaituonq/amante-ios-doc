# Amante IOS Documentation
This app render data from API http://192.168.80.6:3010/v1.0/

## Structure project
- Model
- View Model
- View

### Model 
Create a structure to defined the type of each data
```
// This is a model data of data

struct Banner_Raw: Codable {
    let success: Bool
    let message: String
    let errorCode: Int
    let data: [BannerModel]
}

struct BannerModel: Codable, Identifiable{
    let id: Int
    let bannerName: String
    let bannerType: String
    let fileName: String?
    enum CodingKeys: String, CodingKey {
        case id = "banner_seq"
        case bannerName = "banner_nm"
        case fileName = "file_nm1"
        case bannerType = "banner_type"
    }
}
```
### View Model
Get data from API, The data type will be based on the previously created structural `Model`
```
// This code get data have banner_cd equal to 115, and assign the data to the variable banners

class ProductDetailBannerViewModel: ObservableObject{
    @Published var banners = [BannerModel]()
    
    init(){
        fetchData()
    } 
    
    func fetchData() {
            guard let url = URL(string: "http://192.168.80.6:3010/v1.0/shop/banner/list?banner_cd=115") else { return }
            
            APIManager.shared.downloadData(fromUrl: url) { (returnedData) in
                do {
                    if let data = returnedData {
                        let result = try JSONDecoder().decode(Banner_Raw.self, from: data)
                        DispatchQueue.main.async { [weak self] in
                            self?.banners = result.data
                        }
                    }
                } catch {
                    print("Error decoding JSON: \(error)")
                }
            }
    }
}
```

### View
Get data from `View Model` and render to view
```
// This code handle data and render to view

struct ProductDetailBannerView: View {
    let url: String = "https://www.amante.co.kr/uploads/banner/"
    @StateObject var categoryVM: ProductDetailBannerViewModel = ProductDetailBannerViewModel()
    var body: some View {
        VStack{
            mainTab
        }
    }
    var mainTab: some View{
        VStack{
            TabView {
                ForEach(categoryVM.banners.indices, id: \.self) { i in
                    ZStack(alignment: .bottomTrailing){
                        ZStack{
                            CustomImageViewResize(imageLink:url+"\(categoryVM.banners[i].fileName ?? "c9a6911bd6fba32425768edba97701d2.jpg")")
                                .frame(maxWidth: .infinity)
                                .scaledToFit()
                        }
                        HStack{
                            Text("\(i+1)/\(categoryVM.banners.count)")
                                .foregroundColor(.white)
                                .padding(.horizontal)
                                .padding(.vertical,5)
                                .background(Color.black.opacity(0.5).cornerRadius(15))
                                .padding(5)
                        }
                    }
                }
            }
            .frame(height: 150)
            .tabViewStyle(.page(indexDisplayMode: .never))
        }
        .padding(.horizontal)
    }
}

struct ProductDetailBannerView_Previews: PreviewProvider {
    static var previews: some View {
        ProductDetailBannerView()
    }
}

```