#[cfg(test)]
mod tests {
    use dotenv::dotenv;
    use crate::user::{  user_app::fetch_userinfo_from_file, UserinfoFileResponse};


    #[actix_web::test]
    async fn test_get_userinfo() {
        dotenv().ok();

        let actual = fetch_userinfo_from_file("INFY5".to_string()).unwrap();
        let expected = UserinfoFileResponse {
            id : "INFY5".to_string(),
            name: "Manjula M".to_string()
        };

        assert_eq!(expected,actual);
    }
}
