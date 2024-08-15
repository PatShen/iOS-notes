# === AttributeGraph: cycle detected through attribute 146144 ===

SwiftUI 在布局时，尽可能的使用属性，而非函数来提供数据，例如创建 AttributedString 等稍微有点耗时的数据时

```Swift
# Model 
class MyModel {
  // ✅
  private (set) lazy var termsString: NSMutableAttributedString = {
    let text = "xxxxx"
    NSMutableAttributedString {
      let attributedString: NSMutableAttributedString
      do {
          guard let data = text.data(using: .utf16, allowLossyConversion: true) else {
              return NSMutableAttributedString(string: text)
          }
          attributedString = try NSMutableAttributedString(
              data: data,
              options: [.documentType: NSAttributedString.DocumentType.html],
              documentAttributes: nil)
          let style = NSMutableParagraphStyle()
          style.lineSpacing = 8.0
          let fullRange = NSRange(location: 0, length: attributedString.length)
          attributedString.addAttributes(
              [
                  .paragraphStyle: style,
                  .font: UIFont(xxx) as Any,
                  .foregroundColor: UIColor.smartResPalette.black
              ],
              range: fullRange)
      } catch {
          attributedString = NSMutableAttributedString(string: "")
      }
      return attributedString
  }()

  // ❌
  func termsString() -> NSMutableAttributedString {
    let text = "xxxxx"
    NSMutableAttributedString {
      let attributedString: NSMutableAttributedString
      do {
          guard let data = text.data(using: .utf16, allowLossyConversion: true) else {
              return NSMutableAttributedString(string: text)
          }
          attributedString = try NSMutableAttributedString(
              data: data,
              options: [.documentType: NSAttributedString.DocumentType.html],
              documentAttributes: nil)
          let style = NSMutableParagraphStyle()
          style.lineSpacing = 8.0
          let fullRange = NSRange(location: 0, length: attributedString.length)
          attributedString.addAttributes(
              [
                  .paragraphStyle: style,
                  .font: UIFont(xxx) as Any,
                  .foregroundColor: UIColor.smartResPalette.black
              ],
              range: fullRange)
      } catch {
          attributedString = NSMutableAttributedString(string: "")
      }
      return attributedString
  }
}
```
