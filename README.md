# Amante IOS Documentation
This app render data from API http://192.168.80.6:3010/v1.0/

## Structure project
* [Model](#model)
* [View Model](#view-model)
* [View](#view)
* [Screen](#screens)
* [Package](#package)
* [Setting](#setting)

### Model 
Create a structure to determine the type of each data and use it to decode the JSON
```
// This is a model data of banner

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

### Screens
1. `Login`
2. `Home` [view components](#home-components) 
3. `Category` [view components](#components)
4. `New product` [view components](#components)
5. `Best product` [view components](#components)
5. `Concept room list` [view components](#concept-room-components) (not yet complete)
6. `Campaigns` (not yet complete)
7. `About company`
8. `Infomation of use view`
9. `Terms of service`
10. `Personal data processing`
11. `Sale` [View components](#sale-components)
12. `Pet review list` (not yet complete)
13. `Concept room view` (not yet complete)
14. `Write product review` (not yet complete)
15. `Community` [view components](#community-components)
16. `Best review view` (not yet complete)
17. `Community event detail` (not yet complete)
18. `Sponsored detail` (not yet complete)
19. `Wish list` (not yet complete)
20. `My page`
21. `Edit member information` [view components](#input-components)
22. `Reset password` [view components](#input-components) (not yet complete)
23. `My post` (not yet complete)
24. `My review` (not yet complete)
25. `My unwrite review` [view components](#my-unwrite-review-components) (not yet complete)
26. `My address book management` (not yet complete)
27. `Pet review detail` (not yet complete)
28. `Buying guide detail` (not yet complete)
29. `Order list` (not yet complete)
30. `Order history` (not yet complete)
31. `Cashback point` (not yet complete)
32. `My coupon`
33. `Recently seen products` (not yet complete)
34. `Register coupon`
35. `Personal request`

#### Home components
- `MainBoardBannerView` banner slider
- `CategoryCardView` category card item
- `ProductCardView` product card item
- `PopularkeyWordView` banner keyword slider
- `ConceptRoomListView` room slider
- `ProductDetailBannerView` banner product slider
- `BoardHListView` banner event slider
- `FooterView` footer

#### Components
- `CategoryCardView` category card item
- `ProductCardView` product card item

#### Concept room components
- `ConceptRoomFilter` filter room 

#### Sale components
- `HotDealTimer`  
- `CountdownView`

#### Community components
- `CommunityCostumerReviewView` customer review
- `CommunityEventsListView` event
- `BuyingGuideListView` buy guide
- `SponsoredListView` sponsored
- `BulkPurchaseGuideView` bulk purchase

#### Input components
- `FloatingTextField` input custom
- `FloatingTextFieldWithButton` input custom has button

#### My unwrite review components
- `OrderCardViewTemp` card of review item
- `WriteProductReviewView` write review for product

### Settings
1. Fonts
2. Colors
3. Constants
4. CustomImageView
5. StyleGuides
6. Enums

### Packages
1. `Alamofire` is an HTTP networking library written in Swift.
2. `KakaoOpenSDK`

### Not yet implemented
1. Functions: 
    - Filter: `New Product`, `Best Product`
    - Timer: `Sale`
3. Components: `CommunityCostumerReviewView`