;; prints current working directory
(defn pwd [] (.getAbsolutePath (java.io.File. ".")))
