# Animation



class MainViewController: UIViewController,UIScrollViewDelegate,UITableViewDelegate,UITableViewDataSource,FloatRatingViewDelegate{
   
    
  
    @IBOutlet weak var lblyourTask: UILabel!
    @IBOutlet var lblRating: UILabel!
    @IBOutlet var txtComment: UITextField!
    @IBOutlet var ViewRating: FloatRatingView!
    @IBOutlet var viewRatinData: UIView!
    @IBOutlet var viewratingBg: UIView!
    @IBOutlet var viewORder: UIView!
    @IBOutlet var lblOrder: UILabel!
    @IBOutlet var lblAmount: UILabel!
    @IBOutlet var viewBg: UIView!
   
   
     @IBOutlet var lblNoDataFoundsTwo: UILabel!
     @IBOutlet var lblNoDataFoundsThree: UILabel!
    
    @IBOutlet var viewBgComplete: UIView!
    @IBOutlet var viewBgPernding: UIView!
    @IBOutlet var imgHome: UIImageView!
    @IBOutlet var imgadd: UIImageView!
    @IBOutlet var imgWallet: UIImageView!
    @IBOutlet var imgUser: UIImageView!
    @IBOutlet var viewThree: UIView!
    @IBOutlet var viewtwo: UIView!
    @IBOutlet var viewOne: UIView!
    @IBOutlet var btnConform: UIButton!
    @IBOutlet var btnPending: UIButton!
    @IBOutlet var btnComplete: UIButton!
    @IBOutlet var scroling: UIScrollView!
    @IBOutlet var tblviewConform: UITableView!
    @IBOutlet var tblviewPending: UITableView!
    @IBOutlet var tblviewComplete: UITableView!
    
    @IBOutlet var lblledingConstrein: NSLayoutConstraint!
    
    var IsTost = Bool()
    var viewWidth : CGFloat = 0.0
    var viewHeight : CGFloat = 0.0
    var page : CGFloat = 0.0
    var selectedbtn:Int = 0
    var expectedtoday : CGFloat = 30.0
    var expectedHeightupcoming : CGFloat = 30.0
    
    var ArrayConfirm = [VendorBean]()
    var ArrayPendding = [VendorBean]()
    var ArrayComplete = [VendorBean]()
    let RefreshControllerConfirm = UIRefreshControl()
    
    
    
    var statusdata = ""
    var fk_orders_id = ""
    var VenderID = ""
    var Rating = ""
    var fk_deliveryboy_id = ""
    var IsselectedRating = Bool()
    
     var isApiPendingCall = Bool()
     var isApiConformedCall = Bool()
     var isApiCompleteCall = Bool()
  
        override func viewDidLoad() {
        super.viewDidLoad()
        
        self.isApiCompleteCall = true
        self.isApiPendingCall = true
        self.isApiConformedCall = true

        self.viewORder.isHidden = true
        self.IsselectedRating = false
        
            RefreshControllerConfirm.addTarget(self, action: #selector(self.ORDERHISTORY), for: .valueChanged)
            RefreshControllerConfirm.tintColor = UIColor.black
            tblviewConform.addSubview(RefreshControllerConfirm)
           
           
            
            
            
        let tap = UITapGestureRecognizer(target: self, action: #selector(handleTap))
        tap.delegate = self as? UIGestureRecognizerDelegate
        viewratingBg.addGestureRecognizer(tap)
        self.PENDDINGRating()
    }
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        
        self.btnConform.setTitleColor(UIColor.black, for: .normal)
        self.btnPending.setTitleColor(UIColor.gray, for: .normal)
        self.btnComplete.setTitleColor(UIColor.gray, for: .normal)
        
        self.ViewRating.emptyImage = UIImage(named: "star_Waite")
        self.ViewRating.fullImage = UIImage(named: "star_Black")
        self.ViewRating.delegate = self
        self.ViewRating.contentMode = UIViewContentMode.center
        self.ViewRating.maxRating = 5
        self.ViewRating.minRating = 1
        self.ViewRating.rating = 0
        self.ViewRating.editable = true
        self.ViewRating.halfRatings = true
        self.ViewRating.floatRatings = false
        
        tblviewComplete.delegate = self
        tblviewComplete.dataSource = self
        self.tblviewConform.estimatedRowHeight = 100.0
        self.tblviewConform.rowHeight = UITableViewAutomaticDimension
        self.tblviewPending.estimatedRowHeight = 100.0
        self.tblviewPending.rowHeight = UITableViewAutomaticDimension
        self.tblviewComplete.estimatedRowHeight = 100.0
        self.tblviewComplete.rowHeight = UITableViewAutomaticDimension
       
        
    }
    
    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)
        DispatchQueue.main.async {
            self.SetAnimation()
        }
    }
    @objc func handleTap(sender: UITapGestureRecognizer? = nil) {
        self.viewratingBg.isHidden = true
        self.viewRatinData.isHidden = true
        self.txtComment.text = ""
        self.ViewRating.rating = 0
        view.endEditing(true)
    }
    
    
    
    
    func SetAnimation() {
        scroling.delegate = self
        viewWidth = self.scroling.bounds.size.width
        viewHeight = self.scroling.bounds.size.height 
        self.scroling.contentSize = CGSize(width: viewWidth * 3, height: viewHeight)
        
        self.viewOne.frame = CGRect(x: 0, y:0, width: viewWidth, height:viewHeight)
        
        self.viewtwo.frame = CGRect(x: viewWidth, y:0, width: viewWidth , height: viewHeight)
        
        self.viewThree.frame = CGRect(x: viewWidth*2, y:0, width: viewWidth , height: viewHeight)
        
        if self.viewOne.superview == nil {
            self.scroling.addSubview(self.viewOne)
        }
        if self.viewtwo.superview == nil {
            self.scroling.addSubview(self.viewtwo)
        }
        if self.viewThree.superview == nil {
            self.scroling.addSubview(self.viewThree)
        }
        self.scroling.isPagingEnabled = true
        self.scroling.alwaysBounceVertical = false
        self.view.layoutIfNeeded()
        self.view.layoutSubviews()
        
       // self.scroling.contentOffset = CGPoint(x: page * viewWidth, y: 0)
    }
    //MARK:- ScrolingView Delegate MEthos
    
    func scrollViewDidScroll(_ sender: UIScrollView) {
        if sender != scroling {
            return
        }
        let pageWidth: CGFloat = self.scroling.frame.size.width
        page = self.scroling.contentOffset.x / pageWidth
        if page >= 0.0 && page <= 2.0 {
            lblledingConstrein.constant = (pageWidth / 3.0) * page
        }
    }
    
    func scrollViewDidEndDecelerating(_ scrollView: UIScrollView) {
        if page == 0 {
            selectedbtn = 0
            self.btnConform.setTitleColor(UIColor.black, for: .normal)
            self.btnPending.setTitleColor(UIColor.gray, for: .normal)
            self.btnComplete.setTitleColor(UIColor.gray, for: .normal)
            if isApiConformedCall == true{
                self.isApiConformedCall = false
                DispatchQueue.main.async {
                    self.ORDERHISTORY()
                }
            }
        }
        else if page == 1 {
            selectedbtn = 1
            self.btnConform.setTitleColor(UIColor.gray, for: .normal)
            self.btnPending.setTitleColor(UIColor.black, for: .normal)
            self.btnComplete.setTitleColor(UIColor.gray, for: .normal)
            if isApiPendingCall == true{
                self.isApiPendingCall = false
                DispatchQueue.main.async {
                    self.ORDERHISTORYBYSTATUS()
                }
            }
            
            self.statusdata = "Pending"
        }
        else{
            self.btnConform.setTitleColor(UIColor.gray, for: .normal)
            self.btnPending.setTitleColor(UIColor.gray, for: .normal)
            self.btnComplete.setTitleColor(UIColor.black, for: .normal)
             selectedbtn = 2
            if isApiCompleteCall == true{
                self.isApiCompleteCall = false
                DispatchQueue.main.async {
                    self.ORDERHISTORYBYSTATUS_COMPLETE()
                }
            }
            
            self.statusdata = "Complete"
        }
    }
    func moveToPage(sender : UIButton) {
        let idx = sender.tag
        let pageWidth = self.scroling.frame.size.width * CGFloat(idx)
        self.scroling.scrollRectToVisible(CGRect(x: pageWidth, y: 0, width: viewWidth, height: scroling.bounds.size.height), animated: true)
        self.view.layoutIfNeeded()
    }
    
