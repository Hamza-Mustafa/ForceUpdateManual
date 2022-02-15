# ForceUpdateManual

let APP_ID = "156456456547"

    func checkForAppUpdate() {
        if let config = Environment.config {
            print(config.iosVersion)
            guard let remoteVersion = Double(config.iosVersion), let currentVersion = Bundle.main.version() as? String else { return }
            // check for app update with remote app version
            if remoteVersion > Double(currentVersion) ?? 0.0 {
                // force user to update app
                self.showPopUpToUpdateApp()
            } else {
                // go to login
                self.checkForRegistration()
            }
        }
    }
    
    func showPopUpToUpdateApp() {
        AlertView.showAlert(message: "The installed version of the application is outdated. You need to update the application", actionTitle: "Update") {
            if let url = URL(string: "itms-apps://itunes.apple.com/app/id\(APP_ID)"),
                UIApplication.shared.canOpenURL(url){
                UIApplication.shared.open(url, options: [:], completionHandler: nil)
            }
        }
    }
    
    
    
    
    -------------
    
    import Foundation
import SwiftMessages
import UIKit

class AlertView: UIView {
    
    @IBOutlet weak var bgView: UIView!
    @IBOutlet weak var messageTitle: UILabel!
    @IBOutlet weak var messageButton: UIButton!
    
    var okAction: ((AlertView)-> Void)?
    
    override func awakeFromNib() {
        super.awakeFromNib()
        
        messageButton.layer.cornerRadius = 2.0
        bgView.layer.cornerRadius = 5.0
    }
    
    @IBAction func didPressed(_ sender: Any) {
        okAction?(self)
    }
    
    class func showAlert(message: String, actionTitle: String = "Ok", completion: (() -> Void)? = nil) {
        
        let view: AlertView = try! SwiftMessages.viewFromNib()
        view.messageTitle.text = message
        view.messageButton.setTitle(actionTitle, for: .normal)
        view.backgroundColor = .clear
        
        view.okAction = { customAlert in
            completion?()
            SwiftMessages.hide()
        }
        
        var config = SwiftMessages.Config()
        config.presentationStyle = .center
        config.presentationContext = .window(windowLevel: .alert)
        config.duration = .forever
        config.dimMode = .color(color: UIColor.black.withAlphaComponent(0.6), interactive: false)
        config.interactiveHide = false
        config.preferredStatusBarStyle = .lightContent

        SwiftMessages.show(config: config, view: view)
    }
    }
